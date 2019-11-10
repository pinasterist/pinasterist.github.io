---
layout:     post
title:      "使用GIT实现文件同步功能"
subtitle:   "带版本管理的随处可用的同步工具"
date:       2019-08-02
author:     "pinasterist"
header-img: "img/post-bg-mobilephone.jpg"
tags:
    - git
    - hook
    - sync
---

这是一篇转载的文章，文章出处为[给git配置钩子实现服务器和客户端代码一致](https://blog.laji.cx/post/git-config-hook-to-sync-code/)。只是这文章名字有点词不达意，因此改了个标题放在这里。

* * *

## 给git配置钩子实现服务器和客户端代码一致

这是一篇歪门邪道文章，不按常理走，不喜尽管喷。

这是一篇歪门邪道文章，不按常理走，不喜尽管喷。

一般来说将本地的代码推送到服务器的方式，都是在服务器的仓库用`git init --bare`初始化一个裸仓库，里面只有一些索引和版本信息，有关裸仓库的介绍请自行查找。我基本都是一个人用git，比如这个博客是基于hugo的，我想每次在本地用`git push`之后，服务器上能直接生效，相当于把仓库完全同步了。但是`git init`和`git init --bare`都不能直接这么做。

咋办呢？搜呗。在网上找到了关于git钩子的相关内容，大概就是在你git操作之后会执行你自己写的钩子，那就用这个了。

首先在服务器新建个仓库，而且要专门配置一下`receive.denyCurrentBranch=ignore`，不然你在本地push的时候会报`remote: error: refusing to update checked out branch: refs/heads/master`的错误，因为`git push`只允许推送到裸仓库，具体可以参考这个[stackoverflow](https://stackoverflow.com/questions/12265729/what-are-the-consequences-of-using-receive-denycurrentbranch-in-git)的回答

然后我们来写钩子脚本，有一些没卵用的例子在.git/hooks文件夹下，我们写的脚本也需要放在这里，因为我需要在服务器端写hook，所以对应的应该是写post-receive,这样就会在服务器接手之后执行该脚本

```
#!/bin/sh
GIT_WORK_TREE=$PWD git reset --hard
echo "ok"
```

有时候这个脚本不起作用，可以尝试下面的方案：

```
#!/bin/sh
test "${PWD%/.git}" != "$PWD" && cd ..
unset GIT_DIR GIT_WORK_TREE
git reset --hard
echo "branch updated"
```

然后执行`chmod +x .git/hooks/post-receive` 给它执行权限

最后一句ok没什么用，你写的操作会在客户端推送的时候执行并能看到结果，以remote开头就像这样

```
对象计数中: 3, 完成.
写入对象中: 100% (3/3), 225 bytes | 0 bytes/s, 完成.
Total 3 (delta 0), reused 0 (delta 0)
remote: HEAD 现在位于 4d0425f d
remote: ok
To 45.32.43.30:/home/www/a
   b861b71..4d0425f  master -> master
```

这样就搞定了，梳理一下流程：

服务器init空仓库 > 本地clone空仓库 > 本地添加修改仓库内容 > 本地add commit push > 服务器receive > 服务器看看有没有hook脚本 > 服务器执行post-receive脚本

整个下来之后，本地就和服务器有一模一样的仓库了，在某些场景下非常好用，比如微信开发时需要在本地写代码，但测试时必须要在配置的服务器上调试和测试，此时用这种方法只需要正常使用git控制版本，写完直接push就能看到代码修改后的效果了

最后贴个alias，这样每次在服务器新建个文件夹(仓库)直接执行ginit就搞定上述所有过程了

```
alias ginit='git init \
&& git config receive.denyCurrentBranch ignore \
&& echo -e "#!/bin/sh\nGIT_WORK_TREE=$PWD git reset --hard\n echo \"ok\"" > $PWD/.git/hooks/post-receive \
&& chmod +x $PWD/.git/hooks/post-receive'
```


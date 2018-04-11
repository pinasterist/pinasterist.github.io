---
layout:     post
title:      "如何制作一个程序员的博客"
subtitle:   "一个基于git的博客系统"
date:       2018-04-02
author:     "pinasterist"
header-img: "img/post-bg-blog.jpg"
tags:
    - blog
    - git
    - github
    - hux
---

终于打算开始搭建一个博客了。在网上找了一大圈，最终还是选择github的pages，因为可以用git来写文章。:D

第一步：敲定系统方案

github站点只能存放静态文件，所以根据如何生成网站静态文件，我们可以有以下几种选择：

- jekyll  
    是基于ruby gem的解析引擎，能够将模版、liquid语言、markdown转换为***静态网页***的产生器。
- huxo  
    是基于node.js的***静态网页***产生器。
- 自己手动生成

我选择的是jekyll，仅仅因为github pages默认支持它。

第二步：安装软件

```shell
brew install ruby
gem install jekyll
gem install jekyll-paginate
gem install jekyll-watch
```

第三步：找一个模版

这个就仁者见仁了。我就选了个流行+简洁版，[huxpro](https://github.com/Huxpro/huxpro.github.io)，从hux移植过来的。  
把代码包*pinasterist.github.io-master.zip*下下来备用。（当然你也可以fork它。）

第四步：注册并配置github账号

注册的教程网上一大堆，这里就不赘述了。  
另外，github的博客分主页和项目两种方式。这里我选择的是主页。  
在github账号内创建一个代码仓库，并取名字为pinasterist.github.io 。

第五步：初始化博客的git仓库

1. 展开模版的zip代码包  
    ```shell
unzip pinasterist.github.io-master.zip
```


2. 用git初始化代码目录  
    ```shell
cd pinasterist.github.io-master
git init
```

3. 添加远程仓库  
    ```shell
git remote add origin https://github.com/pinasterist/pinasterist.github.io.git
git push --set-upstream origin master
```

第六步：修改代码

- 更新CNAME

    CNAME里面要填写我的域名

- 


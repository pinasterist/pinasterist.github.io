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

终于打算开始搭建一个博客了。在网上找了一大圈，最终还是选择github的pages，因为可以用git来管理文章。:-D

#### 第一步：敲定系统方案

github站点只能存放静态文件，按照生成网站静态文件的方式，我们可以有以下几种选择：

- jekyll  
    是基于ruby gem的解析引擎，能够将模版、liquid语言、markdown转换为***静态网页***的产生器。
- huxo  
    是基于node.js的***静态网页***产生器。
- 其他静态网页生成器
- 自己手动生成

我选择的是jekyll，仅仅因为github pages默认支持它。

#### 第二步：安装软件

```shell
brew install ruby
gem install jekyll
gem install jekyll-paginate
gem install jekyll-watch
```

#### 第三步：找一个模版

这个就仁者见仁了。我就选了个流行+简洁版，[huxpro](https://github.com/Huxpro/huxpro.github.io)，从hux移植过来的。  
把代码包*pinasterist.github.io-master.zip*下下来备用（当然你也可以fork它）。

#### 第四步：注册并配置github账号

注册的教程网上一大堆，这里就不赘述了。  
另外，github的博客分主页和项目两种方式，我选择的是主页。  
- 在github账号内创建一个代码仓库，并取名字为pinasterist.github.io 。
- 添加密钥（具体请找谷哥或杜娘）。

#### 第五步：初始化博客的git仓库

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

#### 第六步：修改代码

1. 更新CNAME

    CNAME里面要填写我的域名
    ```shell
blog.pinasterist.ml
```
    然后在我的域名管理系统里添加一个CNAME记录，将blog子域名指向pinasterist.github.io。

2. 清除\_post目录中的无用文章

3. 清除img目录中的无用图片

4. 替换img目录中的图片

    - tag-bg.jpg: 分类页面的背景图片

    - home-bg.jpg: 主页面的背景图片

    - about-bg.jpg: 关于页面的背景图片

    - favicon.ico: 网站图标

    - icon_wechat.png: 微信图标

    - avatar-pinasterist.jpg: 我的头像

5. 更新\_config.yml文件

    - 更新title，SEOTitle，email，description，url，keyword

    - 注释所有的通讯方式，RSS设置成false

    - 注释disquse，duoshuo等评论系统（留到以后添加）

    - 注释所有关联朋友（留到以后添加）

#### 第七步：编译

执行下面的命令生成静态网页
```shell
jekyll build
```
如果有编译错误，改正，然后提交，推送到github。
```shell
git add --all
git commit -m 'initial check in'
git push
```

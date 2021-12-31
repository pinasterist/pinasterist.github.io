---
layout:     post
title:      "在APT数据库中查找文件"
subtitle:   "解决不知道某个文件或命令属于哪个软件包的问题"
date:       2021-12-31
author:     "pinasterist"
header-img: "img/post-bg-mobilephone.jpg"
tags:
    - linux
    - apt
    - search
    - package

---

有的时候编译或者安装软件包，会提示缺少某个文件，比如头文件或者动态链接库xxx.so文件等, 虽然我们知道这是因为没有安装某个必须的软件包所致，却不知道具体是哪个软件包。

解决这个问题的方法很多，其中最方便的莫过于apt-file命令。

### 安装apt-file

```
sudo apt-get install apt-file
```

### 更新apt-file缓存

```
apt-file update
```

### 使用apt-files搜索文件依赖

```
apt-file search makeinfo
```

### 使用apt-files罗列软件包中的文件

```
apt-file list textinfo
```


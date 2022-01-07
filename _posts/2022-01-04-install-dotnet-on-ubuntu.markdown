---
layout:     post
title:      "在ubuntu中安装dotnet"
subtitle:   ""
date:       2022-01-04
author:     "pinasterist"
header-img: "img/post-bg-mobilephone.jpg"
tags:
    - linux
    - apt
    - ubuntu
    - dotnet

---

目前，在ubuntu 20.04 LTS版本中，可以安装.net core 3.1, .net 5, .net 6。

首先，安装微软库

```
wget https://packages.microsoft.com/config/ubuntu/20.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb
sudo dpkg -i packages-microsoft-prod.deb
rm packages-microsoft-prod.deb
```

然后，安装.net

```
sudo apt-get update; \
  sudo apt-get install -y apt-transport-https && \
  sudo apt-get update && \
  sudo apt-get install -y dotnet-sdk-6.0
```


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

### 安装libssl1.0

```
vi /etc/apt/sources.list
```

添加下面的库

```
deb [arch=amd64] http://security.ubuntu.com/ubuntu bionic-security main
```

更新并安装

```
apt update
apt install libssl1.0.0
```

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

### 安装微软库

```
wget https://packages.microsoft.com/config/ubuntu/20.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb
sudo dpkg -i packages-microsoft-prod.deb
rm packages-microsoft-prod.deb
```

### 安装.net 6

```
sudo apt-get update; \
  sudo apt-get install -y apt-transport-https && \
  sudo apt-get update && \
  sudo apt-get install -y dotnet-sdk-6.0
```

### 安装libssl1.0.0

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

---
layout:     post
title:      "关闭APT的证书认证"
subtitle:   "某些情况下关闭APT的证书认证"
date:       2022-09-23
author:     "pinasterist"
header-img: "img/post-bg-mobilephone.jpg"
tags:
    - apt
    - key
    - certificate

---

关闭APT的证书认证：

## 临时关闭

需要将下列两个开关打开：

```
Acquire::AllowInsecureRepositories=true
Acquire::AllowDowngradeToInsecureRepositories=true
```

例如，无证书验证的更新缓存命令：
```
apt -o Acquire::AllowInsecureRepositories=true -o Acquire::AllowDowngradeToInsecureRepositories=true update
```

例如，无证书验证的更新安装vim命令：
```
apt -o Acquire::AllowInsecureRepositories=true -o Acquire::AllowDowngradeToInsecureRepositories=true install vim
```

## 永久关闭

编辑apt配置
```
vi /etc/apt/apt.conf.d/99_no-certificate
```

加入这两个开关：
```
Acquire::AllowInsecureRepositories=true
Acquire::AllowDowngradeToInsecureRepositories=true
```

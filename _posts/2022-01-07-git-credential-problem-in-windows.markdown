---
layout:     post
title:      "windows中git无法克隆代码问题"
subtitle:   ""
date:       2022-01-07
author:     "pinasterist"
header-img: "img/post-bg-mobilephone.jpg"
tags:
    - linux
    - apt
    - ubuntu
    - dotnet

---

在windows中安装好git，配置好密钥文件，但是无法克隆代码，git上报的错误是没有权限。

为了排除问题，特意在macos中配置相同的账号、密钥，发现可以克隆成功。所以问题只能落在windows的git环境中，特别是ssh的认证配置。

最终，问题是在于windows中的git使用的ssh是内嵌ssh程序，该程序无法在windows的默认HOME中寻找密钥文件。执行下列命令，指定使用windows自带的ssh程序，就可以解决问题。

```
git config --global core.sshCommand "'C:\Windows\System32\OpenSSH\ssh.exe'"
```

当然，如果强制制定密钥文件，也是可以解决问题的。

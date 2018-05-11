---
layout:     post
title:      "MacOS中的扩展文件属性"
subtitle:   "去除讨厌的@符号"
date:       2018-05-10
author:     "pinasterist"
header-img: "img/post-bg-design.jpg"
tags:
    - macos
    - xattr
---

在下载完用于java加密框架的无限制策略文件，并且放入我的项目中后，git居然无法发现，因而也就无法commit无法备份。用ls命令可以发现文件属性栏多了一个@符号。

```shell
> ls -l US_export_policy.jar
-rw-r--r--@  1 staff  staff   3.0K May 10 14:04 US_export_policy.jar
```

原来这个是macos文件系统中的扩展属性。使用xattr可以查看更详细的信息：

```shell
> xattr -l US_export_policy.jar
com.apple.quarantine: 0081;5af29a42;Chrome;2590B18F-F0CE-4035-A32C-0F5887D81029
```

使用-c命令就可以删除所有扩展属性。

```shell
> xattr -c US_export_policy.jar
```

好了，现在git发现了改动，可以添加并commit了。



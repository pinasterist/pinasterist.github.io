---
layout:     post
title:      "常用和非常用Shell命令"
subtitle:   "Shell百变用法"
date:       2018-08-28
author:     "pinasterist"
header-img: "img/post-bg-mobilephone.jpg"
tags:
    - Shell
    - bash
    - zsh
    - linux
---

对于linux重度用户，写Shell脚本是一门必修课。我们经常花费很多时间查找各种各样的算法、用法。不过很多用法使用频率很少，以至于我们经常感觉明明自己曾经写过一段类似代码，可是就是找不着，不得不用谷歌再次搜索、研究，浪费很多时间和精力。因此，把自己的研究成果积累起来就很有必要了。

这篇文章就是个和Shell有关的大杂烩。我会把所有我用过的Shell用法、技巧、算法等记录在此，免得以后找后悔药。

* 获取随机数
    1. 获取随机二进制数
        rnd=`head -c 10 /dev/urandom`
        注：10表示10个字节

    2. 获取随机HEX字符串
       rnd=`hexdump -n 8 -e '2/4 "%08X" 1 "\n"' /dev/random`
       注：8表示只处理8个输入的字节
           2/4表示读取2次，每次4个字节


---
layout:     post
title:      "SpringBoot中关于logback的一些零碎知识"
subtitle:   "保存常用的logback配置项，便于日后参考"
date:       2021-11-26
author:     "pinasterist"
header-img: "img/post-bg-mobilephone.jpg"
tags:
    - spring boot
    - logback
    - application.yaml
    - logging

---

### 常用logback配置项

```
logging:
  pattern:
    file: "%date{yyyy-MM-dd HH:mm:ss.SSS} - %5.5level %-10.10thread - %20.20logger{20} %-4.4L : %msg%n"
    console: "%date{yyyy-MM-dd HH:mm:ss.SSS} - %5.5level %-10.10thread - %20.20logger{20} %-4.4L : %msg%n"
  file:
    path: "./logs/"
    name: "./logs/my.log"
  level:
    root: INFO
```

### 格式字符串

```
%d{HH:mm:ss.SSS} : 日志输出时间
%thread          : 输出日志的进程名字
%-5level         : 日志级别，并且使用5个字符靠左对齐
%logger{36}      : 日志输出者的名字
%msg             : 日志消息
%n               : 平台的换行符
```

### 颜色支持

使用%clr变量可以给日志加上颜色。

```
%clr(%5p)        : 加色
%clr(%d{yyyy-MM-dd HH:mm:ss.SSS}){yellow}    : 给日期加上黄色
```

支持的颜色种类
```
blue
cyan
faint
green
magenta
red
yellow
```

### 文件输出

文件输出涉及到两个变量：logging.file.name 和 logging.file.path

| logging.file.name | logging.file.path | 例子 | 解释 |
| --- | ----------------------- | -------------------------- |
| (none) | (none) |   | 打印到终端 |
| 文件 | (none) | my.log | 写入到指定文件，文件名可以是绝对路径，也可以是相对路径 |
| (none) | 目录 | /var/log | 在指定目录，写入到spring.log，目录名可以是绝对路径，也可以是相对路径 |


### 文件分割

日志文件自动分割保存涉及到以下5个变量

| 变量名 | 例子 | 描述 |
| - | - | - |
| logging.logback.rollingpolicy.file-name-pattern | MyApp-%d{yyyy-MM-dd}.%i.log | 文件名规则
| logging.logback.rollingpolicy.clean-history-on-start | true | 是否在程序启动的时候清理日志文件
| logging.logback.rollingpolicy.max-file-size | 1MB | 文件的最大容量
| logging.logback.rollingpolicy.total-size-cap | 100MB | 所有日志文件的最大容量
| logging.logback.rollingpolicy.max-history | 7 | 最多保存几个日志文件


### 参考网址

<https://docs.spring.io/spring-boot/docs/current/reference/html/features.html#features.logging>


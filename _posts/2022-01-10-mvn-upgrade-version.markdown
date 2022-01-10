---
layout:     post
title:      "Maven升级版本号"
subtitle:   "用mvn命令管理version"
date:       2022-01-10
author:     "pinasterist"
header-img: "img/post-bg-mobilephone.jpg"
tags:
    - maven
    - version
    - auto
    - management

---

发布新版本的时候，修改maven的配置文件是一件必不可少的工作。mvn特地提供version子命令。简单用法如下：

```
mvn versions:set -DnewVersion=1.1.0-SNAPSHOT

mvn install -N

mvn install

mvn versions:commit
```


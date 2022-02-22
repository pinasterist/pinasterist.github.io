---
layout:     post
title:      "Maven Docker插件的使用"
subtitle:   "用mvn编译、上传Docker镜像"
date:       2022-01-10
author:     "pinasterist"
header-img: "img/post-bg-mobilephone.jpg"
tags:
    - maven
    - docker
    - docker hub

---

Maven通过提供的Docker插件，实现了对Docker镜像的管理。下面是这个插件的简单用法：

```
# build docker
mvn dockerfile:build -Ddockerfile.verbose=TRUE

# push docker
mvn dockerfile:push
```


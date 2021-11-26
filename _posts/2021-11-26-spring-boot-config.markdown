---
layout:     post
title:      "SpringBoot的一些零碎知识"
subtitle:   "保存常用的springboot配置项，便于日后参考"
date:       2021-11-26
author:     "pinasterist"
header-img: "img/post-bg-mobilephone.jpg"
tags:
    - spring boot
    - application.yaml

---

### 关闭内嵌的web服务器

```
spring:
  main:
    web-application-type: "none"
```

### 使用随机的web服务器端口

```
server:
  port: 0
```

测试当前服务器端口值

```
@SpringBootTest(webEnvironment = WebEnvironment.RANDOM_PORT)
public class MyWebIntegrationTests {
    @LocalServerPort
    int port;
    // ...
}
```


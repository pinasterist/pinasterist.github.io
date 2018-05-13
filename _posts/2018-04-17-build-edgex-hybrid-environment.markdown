---
layout:     post
title:      "搭建edgex混合编程环境"
subtitle:   "edgex，第一个边缘IoT系统"
date:       2018-04-17
author:     "pinasterist"
header-img: "img/post-bg-edgex.jpg"
tags:
    - docker
    - edgex
---

Edgex是有20多个微服务构成。绝大部分情况下，开发者只会与一个或若干个微服务打交道，而无需下载所有的源代码。对于不用涉及的微服务，直接用二进制包来辅助测试就可以了。Edgex管这样的编程环境为Hybrid Environment。现在就以device-virtual为例讲讲这个环境如何搭建。

### 第一步：安装edgex
请参看文章[在mac docker上安装edgex foundry](http://blog.pinasterist.ml/2018/04/08/install-edgex-on-macdocker)。

### 第二步：关闭device-virtual

```shell
docker-compose stop device-virtual
```

### 第三步：克隆device-virtual代码

```shell
git clone https://github.com/edgexfoundry/device-virtual.git
```

### 第四步：修改代码

```shell
cd device-virtual
git checkout -b barcelona origin/barcelona
vi src/main/resources/bootstrap.properties
```

设置下列参数

```shell
spring.cloud.consul.host=192.168.8.201
spring.cloud.consul.enabled=true
```

vi src/main/resources/application.properties
设置下列参数
```shell
service.host=192.168.98.1        # 这里改成运行eclipse的主机ip地址
logging.remote.enable=true
logging.remote.url=http://192.168.98.201:48061/api/v1/logs
```
```shell
vi src/main/resources/application.properties
```
将所有localhost替换成192.168.98.201。

### 第五步：编译并启动程序

```shell
mvn clean package
java -jar target/device-virtual-0.5.0-SNAPSHOT.jar
```


---
layout:     post
title:      "mac上安装开发支持用服务器"
subtitle:   "可以做到真正的无网络开发"
date:       2018-04-15
author:     "pinasterist"
header-img: "img/post-bg-design.jpg"
tags:
    - macos
    - docker
    - maven
    - git
    - mongo
    - nexus
---

一个完整的开发环境少不了各种各样的服务器。这里我说一下我是如何在我的mac机器上搭建这些服务器的。

*注意：所有的服务器都是基于docker。请先参看我的文章[在macbook上搭建docker主机](http://blog.pinasterist.ml/2018/04/06/macdocker/)。*

### Maven私有仓库服务器

- 下载nexus镜像
    ```shell
docker pull sonatype/nexus
```

- 制作数据镜像
    ```shell
docker run -d --name nexus-data sonatype/nexus echo 'data-only container for nexus'
```

- 启动nexus服务
    ```shell
docker run -d -p 8081:8081 --name nexus --volumes-from nexus-data sonatype/nexus
```

- 配置nexus服务器

    用浏览器打开http://localhost:8081/nexus，用户名是admin，密码是admin123。

### GIT服务器

- 下载gogs镜像
    ```shell
docker pull gogs/gogs
```

- 制作数据镜像
    ```shell
docker run -d --name gogs-data gogs/gogs echo 'data-only container for gogs'
```

- 启动gogs服务
    ```shell
ocker run -d -p 3022:3022 -p 3000:3000 --name gogs --volumes-from gogs-data gogs/gogs
```

- 配置gogs服务器

    用浏览器打开http://localhost:3000即可出发配置过程。  
    *注意：ssh端口需要改成3022。

### Docker私有仓库服务器

- 下载registry镜像
    ```shell
docker pull registry
```

- 制作数据镜像
    ```shell
docker run -d  --name docker-data registry echo 'data-only container for docker registry'
```

- 启动registry服务
    ```shell
docker run -d -p 5000:5000 --name docker --volumes-from docker-data registry
```

### Mongo数据库服务器

- 下载mongo镜像
    ```shell
docker pull library/mongo
```

- 制作数据镜像
    ```shell
docker run -d --name mongo-data library/mongo echo 'data-only container for mongo'
```

- 启动mongo服务
    ```shell
cker run -d -p 27017:27017 --name mongo --volumes-from mongo-data library/mongo
```

### Redis服务器

- 下载redis镜像
    ```shell
docker pull library/redis
```

- 制作数据镜像
    ```shell
docker run -d --name redis-data library/redis echo 'data-only container for redis'
```

- 启动redis服务
    ```shell
docker run -d -p 6379:6379 --name redis --volumes-from redis-data library/redis
```

### RabbitMQ服务器

- 下载rabbitmq镜像
    ```shell
docker pull docker.io/rabbitmq:3-management
```

- 制作数据镜像
    ```shell
docker run -d --name rabbitmq-data rabbitmq:3-management echo 'data-only container for rabbitmq'
```

- 启动redis服务
    ```shell
docker run -d -p 5672:5672 -p 25672:25672 -p 15672:15672 --name rabbitmq --volumes-from rabbitmq-data rabbitmq:3-management
```

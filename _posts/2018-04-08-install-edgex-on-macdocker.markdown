---
layout:     post
title:      "在mac docker上安装edgex foundry"
subtitle:   "edgex，第一个边缘IoT系统"
date:       2018-04-08
author:     "pinasterist"
header-img: "img/post-bg-edgex.jpg"
tags:
    - docker
    - edgex
---

因为edgex foundery的所有微服务都已经容器化，所以安装起来非常简单。  
下面就是就是具体的步骤：

1. 安装docker环境，具体可以参看[在macbook上搭建docker主机](http://blog.pinasterist.ml/2018/04/06/macdocker/)

2. 克隆edgex的安装项目
    ```shell
git clone https://github.com/edgexfoundry/developer-scripts.git
```

3. 下载所有的docker镜像
    ```shell
cd developer-scripts 
ln -s compose-files/docker-compose-barcelona-0.2.1.yml docker-compose.yml
docker-compose pull
```

4. 启动所有的微服务
    ```shell
docker-compose up -d volume
docker-compose up -d config-seed
docker-compose up -d mongo
docker-compose up -d logging
docker-compose up -d notifications
docker-compose up -d metadata
docker-compose up -d data
docker-compose up -d command   
docker-compose up -d scheduler
docker-compose up -d export-client
docker-compose up -d export-distro
docker-compose up -d rulesengine
docker-compose up -d device-virtual
```

5. 打开consule查看微服务状态

    在浏览器中打开http://docker:8500/ui，查看微服务状态。

*说明*
- 目前edgex的稳定版是barcelona，如果想尝试最新版，将**docker-compose-barcelona-0.2.1.yml**换成**docker-compose-preview-california-0.5.1.yml**。
- 启动微服务时不能太快，两条启动命令之间要有时间间隔。官网上建议是1分钟，实际操作不用这么长，一般15秒差不多了，取决于机器的性能。
- 如果个别服务启动失败，可以执行命令```docker-compose up {name}```查看失败原因。
- edgex提供了一些脚本方便安装使用edgex，可以略作参考。这些脚本都位于developer-scripts项目，包括linux_setup.sh、run-it.sh、update-packages.sh、reset-dockers.sh等。



---
layout:     post
title:      "基于Docker的ubuntu操作系统"
subtitle:   "可以使用docker模拟操作系统"
date:       2022-09-30
author:     "pinasterist"
header-img: "img/post-bg-mobilephone.jpg"
tags:
    - docker
    - ubuntu
    - os

---

基于Docker的ubuntu操作系统

## 创建docker-compose文件

```
version: '2'

services:
  ubuntu:
    container_name: ubuntu
    image: ubuntu:20.04
    restart: unless-stopped
    stdin_open: true
    tty: true
    hostname: ubuntu
```

## 创建辅助脚本

开始脚本
```
cat > start.sh << EOF
#!/usr/bin/env bash

docker-compose down
docker-compose up -d
EOF
chmod 755 start.sh
```

停止脚本
```
cat > stop.sh << EOF
#!/usr/bin/env bash

docker-compose down
EOF
chmod 755 stop.sh
```

贴合脚本
```
cat > attach.sh << EOF
#!/usr/bin/env bash

docker-compose exec ubuntu bash
EOF
chmod 755 attach.sh
```

## 执行

```
./start.sh
./attach.sh
```


---
layout:     post
title:      "Edgex foundry简单介绍"
subtitle:   "edgex，第一个边缘IoT系统"
date:       2018-04-15
author:     "pinasterist"
header-img: "img/post-bg-edgex.jpg"
tags:
    - docker
    - edgex
---

我们知道云计算有个很有名的架构，cloud foundery。那么，在边缘计算中，也有个类似名字，edgex foundery。对了，edgex foundery在边缘计算中的地位就类似于cloud foundery中的地位。

Edgex foundery是Linux基金会牵头开发的，旨在开发一种通用的开放物联网解决方案框架。目前，诞生于2017年的edgex foundery已经拥有了包括Dell、AMD、Bayshore Networks、Beechwoods Software、Canonical、ClearBlade、CloudPlugs、Cloud of Things、VMware以及ZingBox等77名会员，涵盖了硬件制造商、软件开发者、设备制造商，以及系统整合商。它们主要分布在欧洲和北美，中国也有不少。

Edgex foundry框架被设计成可以用于执行在任一硬件或操作系统，以及搭配在任何应用环境当中，并且能跨不同产业应用情境，可以很容易地快速建立不同供应商的连网装置、应用和服务之间的互通性。

Edgex foundry基于微服务架构，包括北向、南向、核心等20多个微服务。请看它的架构图：

![edgex-structure](https://wiki.edgexfoundry.org/download/attachments/328046/EXF_Platform%20Architecture.png?version=1&modificationDate=1493522943000&api=v2)

先看南向。南向的服务叫做device services，顾名思义，就是用来连接IoT硬件的部分。目前他可以连接9种设备接口：
- REST：遵从Restful风格的API
- OPC-UA：
- MODBUS：一种用于工业现场的协议
- BACNET：
- ZIGBEE：
- BLE：
- MQTT：
- SNMP：
- VIRTUAL：这是一个虚拟设备，主要用于开发与调试。

再看北向。北向服务称为export services，就是导出服务。看意思就是将数据转发出去的服务。

中间有core services，就是核心服务，包括：数据、命令、元数据、注册和配置等4种。

另外还有支持服务，主要是整个框架可能会用到的一些功能，包括：规则引擎、计划、警告与通知、日志等。

根据路线图，edgex foundry每半年会出一个版本。每个版本会有一个以人类城市命名的名字。目前，它的开发已经排到了2020年。下面是各个版本的名字和时间。

- 'Barcelona': October 2017
- 'California': ~June 2018
- 'Delhi': ~October 2018
- 'Edinburgh': ~ April 2019
- 'Fuji': ~ October 2019
- 'Geneva': ~ April 2020

截止到2018年4月，Barcelona已经发布，California正在紧锣密鼓地进行。

更多信息，请看Edgex Foundry主页，[https://www.edgexfoundry.org](https://www.edgexfoundry.org)。更多技术细节，请看它的wiki，[https://wiki.edgexfoundry.org](https://wiki.edgexfoundry.org)。



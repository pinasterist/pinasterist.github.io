---
layout:     post
title:      "在macbook上搭建docker主机"
subtitle:   "可控、自由的docker主机"
date:       2018-04-06
author:     "pinasterist"
header-img: "img/post-bg-cluster.jpg"
tags:
    - macos
    - cluster
    - 集群
    - vagrant
    - docker
---

在macbookpro上最流行的docker软件就是那只小鲸鱼了。不过它却有着一些限制：第一个就是性能，当容器数量较多时，很容易卡住；另一个就是网络互通的问题。因为docker是跑在一个定制的轻量级的虚拟机上，与macbook主机之间是NAT网卡，因此docker访问主机上服务很困难，解决起来也很麻烦。所以我干脆在我自己的私人mac集群上分出来一台虚拟机作为docker专用主机。下面是具体的步骤。（至于如何搭建私有集群，请看我的另一篇文章，[在macbook上搭建私有集群](http://blog.pinasterist.ml/2018/04/05/build-maccluster/)。)

第一步：配置DNS
- 添加域名解析到dns配置文件
```shell
vi /usr/local/etc/dnsmasq.conf
```
```
dhcp-host=docker,192.168.98.201,infinite
```
重启dnsmasq
```shell
brew services restart dnsmasq
```
修改hosts文件
```shell
sudo vi /etc/hosts
```
```
192.168.98.201  docker
```

第二步：配置并启动docker主机
- 配置vagrant
```shell
cd vagrant
mkdir docker
cd docker
vi Vagrantfile
```
```
Vagrant.configure("2") do |config|
  config.vm.box = "debian9"
  config.vm.provision "file", source: "./install_docker.sh", destination: "$HOME/install_docker.sh"
  config.vm.provision "shell", do |s|
    s.inline = "sudo bash ./install_docker.sh"
  end
  config.vm.provision "shell", run: "always" do |s|
    s.inline = "sudo ifdown enp0s8 && sudo ifup enp0s8"
  end
  config.vm.hostname = "docker"
  config.vm.network "private_network", name: "vboxnet0", type: "static", ip: "172.28.128.100", auto_config: false
  config.vm.provider "virtualbox" do |v|
    v.name = "docker"
    v.memory = 4096
  end
end
```
```shell
vi install_docker.sh
```
```
#!/bin/bash
apt-get install apt-transport-https dirmngr
echo 'deb https://apt.dockerproject.org/repo debian-stretch main' >> /etc/apt/sources.list
apt-key adv --keyserver hkp://p80.pool.sks-keyservers.net:80 --recv-keys 58118E89F3A912897C070ADBF76221572C52609D
apt-get update
apt-get -y install docker-engine
mkdir -p /etc/systemd/system/docker.service.d
echo "[Service]" > /etc/systemd/system/docker.service.d/override.conf
echo "ExecStart=" >> /etc/systemd/system/docker.service.d/override.conf
echo "ExecStart=/usr/bin/dockerd -H fd:// -H tcp://0.0.0.0:2376" >> /etc/systemd/system/docker.service.d/override.conf
systemctl daemon-reload
systemctl restart docker.service
usermod -G docker vagrant
```
- 启动docker主机
```shell
vagrant up
```
- 免密码配置
```shell
ssh-copy-id -i ~/.ssh/id_rsa.debians.pub vagrant@docker
```
```shell
vi ~/.ssh/config
```
```
Host docker
  User vagrant
  HostName docker
  IdentityFile ~/.ssh/id_rsa.debians
```

第三步：配置mac主机
- 安装软件
```shell
brew install docker
brew install docker-compose
brew install docker-machine
brew install docker-credential-helper
```
- 配置环境变量
```shell
echo 'export DOCKER_HOST="tcp://docker:2376"' >> ~/.zshrc
source ~/.zshrc
```

现在docker已经准备好，用下面的命令测试一下：
```shell
docker ps
```


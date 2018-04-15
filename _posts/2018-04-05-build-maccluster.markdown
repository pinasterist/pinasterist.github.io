---
layout:     post
title:      "在macbook上搭建私有集群"
subtitle:   "打造属于自己的最接近真实的虚拟集群"
date:       2018-04-05
author:     "pinasterist"
header-img: "img/post-bg-cluster.jpg"
tags:
    - macos
    - cluster
    - 集群
    - vagrant
---

现在的后台软件动不动就需要用到集群，因此在开发机上搭建一套虚拟的集群系统就很有必要了。这里，我就说一下我是如何在MacBookPro上做这件事情。

### 第一步：安装软件

1. 安装brew
    ```shell
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```
    加速brew
    ```shell
cd "$(brew --repo)"
git remote set-url origin https://mirrors.ustc.edu.cn/brew.git
cd "$(brew --repo)/Library/Taps/homebrew/homebrew-core"
git remote set-url origin https://mirrors.ustc.edu.cn/homebrew-core.git
echo 'export HOMEBREW_BOTTLE_DOMAIN=https://mirrors.ustc.edu.cn/homebrew-bottles' >> ~/.zshrc
source ~/.zshrc
```

2. 安装cask
    ```shell
brew tap caskroom/cask
```
    加速cask
    ```shell
cd "$(brew --repo)"/Library/Taps/caskroom/homebrew-cask
git remote set-url origin https://mirrors.ustc.edu.cn/homebrew-cask.git
```

3. 安装vagrant
    ```shell
brew install vagrant
```

4. 安装dnsmasq
    ```shell
brew install dnsmasq
```

5. 安装virtualbox
    ```shell
brew cask install virtualbox
```

### 第二步：配置dns

1. 创建hostonly适配器
    ```shell
VBoxManage hostonlyif create
VBoxManage hostonlyif ipconfig vboxnet0 --ip 192.168.98.1 --netmask 255.255.255.0
```
    *注意：请确保创建的是vboxnet0。*

2. 停止bootd服务
    ```shell
launchctl unload /System/Library/LaunchDaemons/bootps.plist
```

3. 删除Virtualbox内置的dhcp服务器
    ```shell
VBoxManage list dhcpservers
VBoxManage dhcpserver remove --netname HostInterfaceNetworking-vboxnet0
```

4. 配置DNS
    ```shell
mv /usr/local/etc/dnsmasq.conf /usr/local/etc/dnsmasq.conf.back
vi /usr/local/etc/dnsmasq.conf
```
    在dnsmasq.conf中写入下列文字
    ```shell
interface=vboxnet0
except-interface=lo0
bind-interfaces
strict-order
no-hosts
dhcp-range=192.168.98.32,192.168.98.64,255.255.255.0,12h
dhcp-option=6,192.168.98.1
dhcp-host=debian01,192.168.98.101,infinite
dhcp-host=debian02,192.168.98.102,infinite
dhcp-host=debian03,192.168.98.103,infinite
dhcp-host=debian04,192.168.98.104,infinite
dhcp-host=debian05,192.168.98.105,infinite
```

5. 重启dnsmasq
    ```shell
brew services restart dnsmasq
```

6. 打开macos的ip转发
    ```shell
sysctl -w net.ipv4.ip_forward = 1
```

7. 打开macos的nat
    ```shell
vi /etc/pf.conf
```
    在pf.conf文件中加入下列带注释的文本
    ```
scrub-anchor "com.apple/*"
nat-anchor "com.apple/*"
nat on en0 from vboxnet0:network to any -> (en0)  # insert this line
nat on en6 from vboxnet0:network to any -> (en6)  # insert this line
rdr-anchor "com.apple/*"
dummynet-anchor "com.apple/*"
anchor "com.apple/*"
load anchor "com.apple" from "/etc/pf.anchors/com.apple"
pass from 192.168.98.0/24 to any keep state       # insert this line
```
    执行下列命令使之生效
    ```shell
pfctl -d
pfctl -f /etc/pf.conf
pfctl -e
```

### 第三步：准备box文件

1. 下载debian9的iso
    ```shell
wget http://mirrors.163.com/debian-cd/current/amd64/iso-cd/debian-9.4.0-amd64-netinst.iso
```

2. 安装debian9
- 虚拟机的名字是debian9
- 确保只有一个网卡，且该网卡是NAT类型
- 设置root密码为vagrant
- 创建普通用户vagrant，并设置密码为vagrant

3. 配置debian9
- 以root身份登入debian9，然后执行下面的命令：
    ```shell
groupadd admin
usermod -a admin vagrant
apt-get install sudo
```
    更改grub参数
    ```shell
vi /boot/grub/grub.cfg
```
    在grub.cfg文件中修改对应下列文本的相应行
    ```
set timeout=1
```
    更改sudo参数
    ```shell
vi /etc/sudoers
```
    在sudoers文件中加入下列文本
    ```
Defaults env_keep="SSH_AUTH_SOCK"
%admin ALL=NOPASSWD: ALL
```
    更改network参数
    ```shell
vi /etc/network/interfaces
```
    在interfaces文件中加入下面的文本
    ```
auto enp0s3
iface enp0s3 inet dhcp
allow-hotplug enp0s8
iface enp0s8 inet dhcp
```
    更改apt repo参数
    ```shell
vi /etc/apt/sources.list
```
    在sources.list文件中加入下列一行
    ```
deb http://ftp.debian.org/debian stretch-backports main contrib
```
    安装virtualbox client
    ```shell
apt update
apt install virtualbox-guest-utils
```
- 以vagrant身份登入debian9，然后执行下面的命令：
    ```shell
mkdir .ssh
cd ~/.ssh
wget http://github.com/mitchellh/vagrant/raw/master/keys/vagrant
wget http://github.com/mitchellh/vagrant/raw/master/keys/vagrant.pub
cp vagrant.pub authorized_keys
chmod 600 vagrant
chmod 600 authorized_keys
```
- 最后清理并关闭虚拟机
    ```shell
sudo apt-get clean
sudo poweroff
```

4. 制作debian9镜像文件
    ```shell
vagrant package --output debian9.box --base debian9
vagrant box add --name debian9 ./debian9.box
```

### 第四步：配置并启动集群
- 编辑集群文件
    ```shell
mkdir -p vagrant/debians
cd vagrant/debians
vi Vagrantfile
```
    在Vagrantfile中加入下列文本
    ```
Vagrant.configure("2") do |config|
  config.vm.box = "debian9"
  config.vm.provision "shell", run: "always" do |s|
    s.inline = "sudo ifdown enp0s8 && sudo ifup enp0s8"
  end
  config.vm.define "debian01" do |d|
    d.vm.hostname = "debian01"
    d.vm.network "private_network", name: "vboxnet0", type: "static", 
      ip: "172.28.128.100",  mac: "0800270ca02d", auto_config: false
    d.vm.provider "virtualbox" do |v|
      v.name = "debian01"
    end
  end
  config.vm.define "debian02", autostart: false do |d|
    d.vm.hostname = "debian02"
    d.vm.network "private_network", name: "vboxnet0", type: "static", 
       ip: "172.28.128.100",  mac: "08002704ec7b", auto_config: false
    d.vm.provider "virtualbox" do |vb|
      vb.name = "debian02"
    end
  end
  config.vm.define "debian03", autostart: false do |d|
    d.vm.hostname = "debian03"
    d.vm.network "private_network", name: "vboxnet0", type: "static", 
       ip: "172.28.128.100",  mac: "080027416c74", auto_config: false
    d.vm.provider "virtualbox" do |vb|
      vb.name = "debian03"
    end
  end
  config.vm.define "debian04", autostart: false do |d|
    d.vm.hostname = "debian04"
    d.vm.network "private_network", name: "vboxnet0", type: "static", 
       ip: "172.28.128.100",  mac: "080027f8c821", auto_config: false
    d.vm.provider "virtualbox" do |vb|
      vb.name = "debian04"
    end
  end
  config.vm.define "debian05", autostart: false do |d|
    d.vm.hostname = "debian05"
    d.vm.network "private_network", name: "vboxnet0", type: "static", 
       ip: "172.28.128.100",  mac: "0800276dedca", auto_config: false
    d.vm.provider "virtualbox" do |vb|
      vb.name = "debian05"
    end
  end
end
```
    启动集群
    ```shell
vagrant up debian01
vagrant up debian02
vagrant up debian03
vagrant up debian04
vagrant up debian05
```
    *如果不提供名字，默认只启动第一个*

### 第五步：其它配置
1. 更新hosts文件
    ```shell
sudo vi /etc/hosts
```
    在hosts中添加下列文本
    ```
192.168.98.101  debian01
192.168.98.102  debian02
192.168.98.103  debian03
192.168.98.104  debian04
192.168.98.105  debian05
    ```
    这样我就可以用命令```ssh vagrant@debian01```直接登录虚拟主机。

2. 免密码登录设置
    ```shell
ssh-copy-id -i ~/.ssh/id_rsa.debians.pub vagrant@debian01
```

到这里我就得到了由5台虚拟机组成的虚拟集群。接下来，我可以安装集群管理软件来管理它们，例如puppet，chef，ansible等等。

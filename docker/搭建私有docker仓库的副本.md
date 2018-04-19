---
title: 搭建私有docker仓库
date: 2017-07-20 18:24:20
categories:
tags: docker
---

# 搭建私有docker仓库

我在学习docker的过程中遇到了一个巨麻烦的问题：GFW
他阻止了我docker login的流畅，因此想到在自己的服务器上搭建一个docker仓库，不但为了隐私的使用，也为了更好的装逼。

### 在 centOS 7 上 安装docker

首先因为是自用，所以 docker ce 足够了
[官方教程](https://docs.docker.com/engine/installation/linux/docker-ce/centos/#prerequisites)

**0 - 删除旧版本**

```bash
sudo yum remove docker \
                docker-common \
                docker-selinux \
                docker-engine
```

#### **1 - install docker cd**
在新主机上首次安装Docker CE之前，需要设置Docker存储库。之后可以从存储库安装和更新Docker。

**1.1 - set up the repository**
  1. 安装依赖包
     `yum-utils` 提供了 `yum-config-manager` 工具，`device-mapper-persistent-data` 和 `lvm2` 是 `devicemapper` 存储驱动程序所要求的
     ```bash
       sudo yum install -y yum-utils device-mapper-persistent-data lvm2
     ```
  2. 安装稳定版
     ```bash
     sudo yum-config-manager \
          --add-repo \
          https://download.docker.com/linux/centos/docker-ce.repo
     ```

**1.2 - install Docker CE**
  1. 更新 `yum` 包索引
    ```bash
    sudo yum makecache fast
    ```
    如果这是第一次从添加 Docker repositories 后刷新包索引，系统将提示您接受GPG密钥，并显示密钥的 fingerprint。
    fingerprint 应符合 `060A 61C5 1B55 8A7F 742B 77AA C52F EB6B 621E 9F35`。
  2. 安装最新的 Docker CE
     ```bash
     sudo yum install docker-ce
     ```
  3. 升级 docker
     先运行`sudo yum makecache fast`
     再走安装步骤即可

#### **2 - deploy registry image**
docker 官方有提供 registry 镜像用于部署私有仓库

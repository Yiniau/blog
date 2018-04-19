---
layout: docker
title: docker笔记_1
date: 2017-05-25 18:42:23
tags: docker
---

#### 主要参考资料

[官方文档](https://docs.docker.com/get-started/#container-diagram)
[docker从入门到实践](https://yeasy.gitbooks.io/docker_practice/content/introduction/what.html)

#### 安装

我是用的是mac，所以直接使用`brew install docker`就能顺利安装

```bash
$ docker -v
$ Docker version 17.03.1-ce, build c6d412e
```

#### 先决知识

 - IP Addresses and Ports        || IP地址和端口
 - Virtual Machines              || 虚拟机
 - Editing configuration files   || 编辑配置文件
 - Basic familiarity with the ideas of code dependencies and building || 基本熟悉代码依赖和构建的思想
 - Machine resource usage terms, like CPU percentages, RAM use in bytes, etc. || 机器资源使用条款，如CPU百分比，RAM使用字节数等

#### containers and images || <容器>和<镜像>

**image** 镜像是一个轻量级的、独立的、可执行的 package ，包含了所有运行一个程序需要的一切，包含 code,a runtime,libraries,environment variables,and config files。

> 我们都知道，操作系统分为内核和用户空间。对于 Linux 而言，内核启动后，会挂载 root 文件系统为其提供用户空间支持。而 Docker 镜像（Image），就相当于是一个 root 文件系统。比如官方镜像 ubuntu:14.04 就包含了完整的一套 Ubuntu 14.04 最小系统的 root 文件系统。

因为镜像包含操作系统完整的 root 文件系统，其体积往往是庞大的，因此在 Docker 设计时，就充分利用 [Union FS](https://en.wikipedia.org/wiki/Union_mount) 的技术，将其设计为分层存储的架构。所以严格来说，镜像并非像是一个ISO那样的打包文件，而是一个虚拟的概念。
镜像构建时，会一层层构建，前一层是后一层的基础。每一层构建完就不会再发生改变，后一层上的任何改变只发生在自己这一层。比如，删除前一层文件的操作，实际不是真的删除前一层的文件，而是仅在当前层标记为该文件已删除。在最终容器运行的时候，虽然不会看到这个文件，但是实际上该文件会一直跟随镜像。因此，在构建镜像的时候，需要额外小心，每一层尽量只包含该层需要添加的东西，任何额外的东西应该在该层构建结束前清理掉。

> 分层存储的特征还使得镜像的复用、定制变的更为容易。甚至可以用之前构建好的镜像作为基础层，然后进一步添加新的层，以定制自己所需的内容，构建新的镜像。

**container** 容器是**一个**运行时镜像的**实例** - what the images becomes in memory when actully executed.
默认情况下，它与主机环境完全隔离，只能访问主机文件和端口（如果配置为这样做）。(端口和文件的访问都需要经过配置)
容器运行在主机的内核上，可以说容器的实质是进程，但是与普通进程不同的是容器拥有独立的[命名空间](https://en.wikipedia.org/wiki/Linux_namespaces),因此容器可以拥有自己的 root 文件系统、自己的网络配置、自己的进程空间，甚至自己的用户 ID 空间。容器内的进程是运行在一个隔离的环境里，使用起来，就好像是在一个独立于宿主的系统下操作一样。
容器与镜像一样也使用了分层储存。
每一个容器运行时都是以镜像为基础层，在其上创建一个为容器运行时读写准备的储存层，可以称为**容器储存层**
当容消亡时容器储存层同样消失，任何保存在容器储存层中的数据都会丢失。
按照 [Docker 最佳实践]()的要求，容器不应该向其存储层内写入任何数据，容器存储层要保持无状态化。所有的文件写入操作，都应该使用 数据卷（Volume）、或者绑定宿主目录，在这些位置的读写会跳过容器存储层，直接对宿主(或网络存储)发生读写，其性能和稳定性更高。

#### Containers 与传统虚拟机的比较

**Virtual Machine diagram**

```
          |=========|
          |   VM    |
|---------|---------|---------|
|  App A  |  App B  |  App C  |
|---------|---------|---------|
|Bins/Libs|Bins/Libs|Bins/Libs|
|---------|---------|---------|
| GuestOS | GuestOS | GuestOS |
|         |=========|         |
|=============================|
|          Hypervisor         |
|-----------------------------|
|          Host   OS          |
|-----------------------------|
|       Infrastructure        |
|-----------------------------|
```

**Container diagram**

```
        |=============|
        |  CONTAINER  |
        |_           _|
|---------|---------|---------|
|  App A  |  App B  |  App C  |
|---------|---------|---------|
|Bins/Libs|Bins/Libs|Bins/Libs|
|         |=========|         |
|=============================|
|        Docker Engine        |
|-----------------------------|
|          Host  OS           |
|-----------------------------|
|       Infrastructure        |
|-----------------------------|
```

传统虚拟机技术会虚拟一整套硬件，并在其上运行一个完整的操作系统，之后再运行应用程序
而容器内的应用直接运行于宿主机的内核上，容器没有自己的内核,多个容器可以共享一个内核。
容器也不进行硬件模拟，因此容器要比虚拟机**轻快**不少。

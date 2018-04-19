---
title: 使用docker配置开发端环境
date: 2017-07-18 22:14:10
categories:
tags: docker
---

# 使用docker配置开发环境

`last updated: 2017-07-22`

目前只在本地开发环境使用过，也只记录到这里

### docker 搭建 react 项目的本地开发环境

#### 安装 docker

对于mac安装docker很简单，下载docker for mac直接安装就行，会自动安装`docker-compose`、`docker-machine`。

记录时的docker版本为：
```bash
❯ docker -v
Docker version 17.06.0-ce, build 02c1d87
```

#### pull node

本地自用，没有什么历史包袱，就直接用了8.0.0的，大版本前都不会有api大改，就直接用了 `node:8`

```bash
docker pull node:8
```

#### 定制image

##### Dockerfile

作为前端react项目的本地开发环境，不需要太多复杂的东西，实现的目标是从github之类的地方clone或者download下来的项目可以直接运行在环境内：

```docker
# base image
FROM node:8

WORKDIR /app

# install something
RUN npm i -g yarn \
 && yarn install

# export 3000
EXPORT 3000

# start commond
# need a start script
CMD yarn run start
```

##### 制作image

```bash
docker build react-dev-server .
```

#### 启动命令

以本地目录作为数据卷挂载到容器内，并映射端口，开启伪tty和标准输入流
```bash
docker run \
-p 3000:3000 \
-v ~/path/to/project:/app \
-t \
-i \
react-dev-server
```

访问localhost:3000就能正常浏览

> 需要注意的是 使用过 0.13 左右的老版本的 yarn 会遇到权限问题，这是yarn的问题，它在创建node_moudle文件时的权限设置为 `-rwx------` 我使用的 0.27 已经没有这个问题了，项目中已存在的node_moudle保险起见全删了重新下就好

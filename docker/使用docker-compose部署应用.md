---
layout: docker
title: docker笔记_1
date: 2017-05-25 18:42:23
tags: docker
---

# 使用 docker-compose 部署应用

万事开始之前
docker版本号为 `17.06.0-ce`
docker-compose版本号为 `1.14.0`

并创建一个基础的`docker-compose.yml`文件

```yaml
version: '3'

services:
  mongodb:
    image: mongo:3.5
    container_name: "mongodb"
    environment:
      - MONGO_DATA_DIR=/data/db
      - MONGO_LOG_DIR=/dev/null
    volumes:
      - ~/db/mongo-data:/data/db
    ports:
      - 27017:27017
    command: mongod --smallfiles --logpath=/dev/null # --quiet
```

## 使用mongodb

进入contanier
```bash
docker-compose exec mongodb [bash|mongo]
# 直接使用 mongo 会更方便一点
```
创建 blog 数据库
```bash
> use blog
```

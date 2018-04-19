---
title: docker笔记_2_Dockerfile
date: 2017-05-26 13:44:26
categories: docker
tags: docker
---

# Dockerfile

image 采用Union-FS分层储存，通过 `docker commit` 可以保存容器的储存层为一个新的镜像，也就是将运行时储存层持久化为 image 中的一层结构

但是直接使用 commit 会出现诸多问题，使用 `docker diff <container name|ID|shareID>` 可以观察到commit之前之后的变动，在一个命令被执行后会有大量无关内容被添加，特别是一些安装软件的命令。直接使用 commit 很容易造成 image 臃肿，使用 commit 生成的 image 也被称为 **黑箱镜像**（生成过程不透明）。

docker 官方最佳实践的做法为使用 **Dockerfile** 定制image

#### 主要指令

- FROM 制定基础镜像
- RUN  执行命令
- COPY 复制文件
- ADD  高级的复制文件指令
- CMD  容器启动命令
- ENTRYPOINT 入口文件
- ENV  设置环境变量
- ARG  构建参数
- VOLUME 定义匿名卷（数据卷）
- EXPOSE 暴露接口
- WORKDIR 制定工作目录
- USER    制定当前用户
- HEALTHCHECK 健康检查
- ONBUILD 为他人做嫁衣

在 Dockerfile 中每出现一个 `RUN` 就会多构建一层镜像

#### 使用 Dockerfile 定义 container

```bash
FROM debian:jessie

RUN apt-get update
RUN apt-get install -y gcc libc6-dev make
RUN wget -O redis.tar.gz "http://download.redis.io/releases/redis-3.2.5.tar.gz"
RUN mkdir -p /usr/src/redis
RUN tar -xzf redis.tar.gz -C /usr/src/redis --strip-components=1
RUN make -C /usr/src/redis
RUN make -C /usr/src/redis install
```

上面的 Dockerfile 会新建7层镜像，多出了许多没有意义的，运行时不需要的东西。

这些RUN命令的目的是编译安装`redis`可执行文件，只需要用1层代替即可。

```bash
FROM debian:jessie

RUN buildDeps='gcc libc6-dev make' \
    && apt-get update \
    && apt-get install -y $buildDeps \
    && wget -O redis.tar.gz "http://download.redis.io/releases/redis-3.2.5.tar.gz" \
    && mkdir -p /usr/src/redis \
    && tar -xzf redis.tar.gz -C /usr/src/redis --strip-components=1 \
    && make -C /usr/src/redis \
    && make -C /usr/src/redis install \
    && rm -rf /var/lib/apt/lists/* \
    && rm redis.tar.gz \
    && rm -r /usr/src/redis \
    && apt-get purge -y --auto-remove $buildDeps
```

使用 `&&` 将所需命令串街起来，简化为1层。
Dockerfile 支持使用 `\` 对命令进行换行，格式化命令代码。


下面是一个大体的例子

```bash
# Use an official Python runtime as a base image
# 使用一个官方的 Python runtime 作为基础镜像
FROM python:2.7-slim

# Set the working directory to /app
# 将工作目录设置到 /app
WORKDIR /app

# Copy the current directory contents into the container at /app
# 复制当前目录下的内容到容器文件系统的 /app 下
ADD . /app

# Install any needed packages specified in requirements.txt
# 安装任何需要的在 requirements.txt 中说明的 packages
RUN pip install -r requirements.txt

# Make port 80 available to the world outside this container
# 将 80 端口暴露给 contanier 外部
EXPOSE 80

# Define environment variable
# 定义环境变量
ENV NAME World

# Run app.py when the container launches
# 当 container 启动是运行 app.py
CMD ["python", "app.py"]
```

这个 `Dockerfile` 文件还包含了几个我们还没有创建的文件，即 `app.py` 和 `requirements.txt`。

接下来就是完成这两个文件了

#### 应用本身

抓取将这两个文件并将他们放在 dockerfile 所在的文件夹下。

这就已经完成了一个应用，十分简单。

> 当前面的 `Dockerfile` 被 build 成 image 时，`app.py` 和 `requirements.txt` 会被 `ADD` 命令添加，而 `app.py` 的输出则能通过 `EXPOSE` 命令使外部能够通过 HTTP 访问。

**requriements.txt**

```txt
Flask
Redis
```

**app.py**

```python
from flask import Flask
from redis import Redis, RedisError
import os
import socket

# Connect to Redis
redis = Redis(host="redis", db=0, socket_connect_timeout=2, socket_timeout=2)

app = Flask(__name__)

@app.route("/")
def hello():
    try:
        visits = redis.incr("counter")
    except RedisError:
        visits = "<i>cannot connect to Redis, counter disabled</i>"

    html = "<h3>Hello {name}!</h3>" \
           "<b>Hostname:</b> {hostname}<br/>" \
           "<b>Visits:</b> {visits}"
    return html.format(name=os.getenv("NAME", "world"), hostname=socket.gethostname(), visits=visits)

if __name__ == "__main__":
	app.run(host='0.0.0.0', port=80)
```

现在将看到 `pip install -r requirements.txt` 会为Python安装Flask和Redis库，并且app将会打印出环境变量 `NAME`，并且输出`socket.gethostname()`的结果。
最终，由于 Redis 没有在运行(因为只安装了Python库，并不是Redis自身)，我们能够预计尝试使用它的时候会失败并产生错误信息。

#### Build the App

构建开始前在系统中并不需要Python或者其他任何在requirements.txt文件中求的东西，也不会将用于构建image的库安装到主机系统上。

运行编译命令

```bash
docker build -t friendlyhello .
```

查看images

```bash
docker images
```

```bash
docker images

REPOSITORY                                  TAG                 IMAGE ID            CREATED             SIZE
friendlyhello                               latest              d5a69ab6bd43        24 hours ago        195 MB
```

运行后会出现一个个通知，指出Python正在 http://0.0.0.0:80 为你的应用程序提供服务。 但是，该消息来自容器内部，并不知道你通过 `EXPOSE` 将该容器的80端口暴露并在启动命令中remapping80到4000，正确的URL为 **http：// localhost：4000**。 在浏览器中访问，你会看到“Hello World”文本，容器ID和Redis错误信息。

#### run 应用

```bash
docker run -p 4000:80 friendlyhello
```

将会在运行 friendlyhello ，并将在 Dockerfile 中使用 `EXPOSE` 暴露的 80 端口并重映射到 4000。

运行之后将提示信息，提示在 `http://0.0.0.0:80` 中运行服务，但这是在 container 中的地址，主机访问的正确地址为 `http://localhost:4000`。

**后台运行 | 分离模式**

```bash
docker run -d -p 4000:80 friendlyhello
```

运行后会得到一条巨长的 container ID,可以使用 `docker ps` 查看

```bash
docker ps

CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS                  NAMES
ed4cab2deb90        friendlyhello       "python app.py"     24 hours ago        Up About a minute   0.0.0.0:4000->80/tcp   sad_curie
```
使用 `docker stop ed4cab2deb90` 即可停止运行。

#### 命令总结

```bash
docker build -t friendlyname .  # Create image using this directory's Dockerfile
docker run -p 4000:80 friendlyname  # Run "friendlyname" mapping port 4000 to 80
docker run -d -p 4000:80 friendlyname         # Same thing, but in detached mode
docker ps                                 # See a list of all running containers
docker stop <hash>                     # Gracefully stop the specified container
docker ps -a           # See a list of all containers, even the ones not running
docker kill <hash>                   # Force shutdown of the specified container
docker rm <hash>              # Remove the specified container from this machine
docker rm $(docker ps -a -q)           # Remove all containers from this machine
docker images -a                               # Show all images on this machine
docker rmi <imagename>            # Remove the specified image from this machine
docker rmi $(docker images -q)             # Remove all images from this machine
docker login             # Log in this CLI session using your Docker credentials
docker tag <image> username/repository:tag  # Tag <image> for upload to registry
docker push username/repository:tag            # Upload tagged image to registry
docker run username/repository:tag                   # Run image from a registry
```

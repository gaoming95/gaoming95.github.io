---
title: Docker基础
subtitle: Docker 是一个开源的应用容器引擎，基于 Go 语言 并遵从 Apache2.0 协议开源。Docker 可以让开发者打包他们的应用以及依赖包到一个轻量级、可移植的容器中，然后发布到任何流行的 Linux 机器上，也可以实现虚拟化
date: 2020-09-05
author: 高明
tags:
	- 运维
---



# Docker基础

## Docker 基础

### Ubuntu Docker 安装

```bash
curl -fsSL https://get.docker.com | bash -s docker --mirror Aliyun
```

### docker 切镜像源

可参考阿里镜像加速

1. 创建文件夹

   ```bash
   sudo mkdir -p /etc/docker
   ```

2. 编辑

   ```bash
   sudo vi /etc/docker/daemon.json
   ```

   ```json
   {
     "registry-mirrors": ["https://registry.docker-cn.com"]
   }
   ```

3. 重启

   ```bash
   sudo systemctl daemon-reload
   sudo systemctl restart docker
   ```

### docker免sudo

```bash
sudo usermod -aG docker $USER
```

### 启动docker服务

```bash
# service 命令的用法
sudo service docker start
# systemctl 命令的用法
sudo systemctl start docker
```

## Image文件

```bash
# 列出本机的所有 image 文件。
docker image ls
# 删除 image 文件
docker image rm [imageName]
```

```bash
# 运行image文件
docker container run hello-world
# 如果镜像不存在，会从远程仓库抓取
# 容器会自动停止
```

```bash
# 容器不会自动终止
docker container run -it ubuntu bash
# 手动停止容器
docker container kill [containID]
```

## 容器文件

容器一旦生成，就会同时存在两个文件，Image文件和容器文件。

关闭容器并不会删除容器文件，只是容器停止运行

```bash
# 列出正在运行的容器
docker container ls
# 列出本机所有容器，包括终止运行的容器
docker container ls -all
# 终止容器
docker container kill
# 删除容器
docker cintainer rm [containerID]
```

## 制作DockerFile文件

【示例】制作Dockerfile文件

```
git clone https://github.com/ruanyf/koa-demos.git
cd koa-demos
```

### 编写Dockerfile文件

1. 在项目的根目录下，新建一个文本文件`.dockerignore`，写入如下内容

   ```bahs
   .git
   node_modules
   npm-debug.log
   ```

   上述文件表示三个文件需要排除，不需要打包至image文件

2. 在项目的根目录下，新建一个文本文件Dockerfile

   ```
   FROM node:8.4
   COPY . /app
   WORKDIR /app
   RUN npm install --registry=https://registry.npm.taobao.org
   EXPOSE 3000
   
   FROM node:8.4：该 image 文件继承官方的 node image，冒号表示标签，这里标签是8.4，即8.4版本的 node。
   COPY . /app：将当前目录下的所有文件（除了.dockerignore排除的路径），都拷贝进入 image 文件的/app目录。
   WORKDIR /app：指定接下来的工作路径为/app。
   RUN npm install：在/app目录下，运行npm install命令安装依赖。注意，安装后所有的依赖，都将打包进入 image 文件。
   EXPOSE 3000：将容器 3000 端口暴露出来， 允许外部连接这个端口。
   ```

### 创建image文件

使用docker image build命令创建image文件

```bash
$ docker image build -t koa-demo .
# 或者
$ docker image build -t koa-demo:0.0.1 .
```

上面代码中，`-t`参数用来指定 image 文件的名字，后面还可以用冒号指定标签。如果不指定，默认的标签就是`latest`。最后的那个点表示 Dockerfile 文件所在的路径，上例是当前路径，所以是一个点。

如果运行成功，就可以看到新生成的 image 文件`koa-demo`了。

```
docker image ls
```

### 生成容器

```bash
$ docker container run -p 8000:3000 -it koa-demo /bin/bash
# 或者
$ docker container run -p 8000:3000 -it koa-demo:0.0.1 /bin/bash

-p参数：容器的 3000 端口映射到本机的 8000 端口。
-it参数：容器的 Shell 映射到当前的 Shell，然后你在本机窗口输入的命令，就会传入容器。
koa-demo:0.0.1：image 文件的名字（如果有标签，还需要提供标签，默认是 latest 标签）。
/bin/bash：容器启动以后，内部第一个执行的命令。这里是启动 Bash，保证用户可以使用 Shell。
```

```
root@66d80f4aaf1e:/app# node demos/01.js
```

### CMD命令

```
手动输入命令node demos/01.js。我们可以把这个命令写在 Dockerfile 里面
```

## portainer

```
docker run -d -p 9000:9000 -v /var/run/docker.sock:/var/run/docker.sock portainer/portainer
```

## mysql

```
docker run -itd --name mysql-test -p 3306:3306 -e MYSQL_ROOT_PASSWORD=root mysql
```




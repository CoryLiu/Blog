---
title: Docker常用命令
date: 2020-01-05 12:28:07
tags:
- Docker
categories:
- Docker
---

简单记录一下初学的内容
<!-- more -->

### 启动容器：
```bash
$ docker run IMAGE [COMMAND] [ARG...]
```
- docker run ubuntu echo 'hello world'
  
### 启动交互式容器：
```bash
$ docker run -i -t IMAGE /bin/bash
```
- -i --interactive=true | false 默认 false
- -t --tty=true | false 默认 false

### 查看容器：
```bash
$ docker ps [-a] [-l]
$ docker inspect [容器名字，容器id]
```

### 重新启动停止的容器：
```bash
$ docker start [-i] 容器名
```

### 进入容器【进入容器命令行】
```bash
$ docker exec -it [容器名，容器id] bash
```

### 删除停止的容器：
```bash
$ docker rm 容器id
```

### 守护式容器

```bash
$ docker run -d 镜像名 [COMMAND] [ARG...]
```

---

### 运行docker
```bash
$ docker run -di --name=mynginx -p 8002:80 nginx
```
- 映射本地的8002端口

### 进入docker容器命令行

```bash
$ docker exec -it 镜像名 /bin/bash
```

### 部署静态页面到docker
- 当前系统bash在vue项目的dist目录下
- 拷贝到名为mynginx的docker的默认html目录下
```bash
$ docker cp ./ mynginx:/usr/share/nginx/html
```

## 迁移与备份

1. 将容器保存为镜像
    ```bash
    $ docker commit mynginx mynginx_i
    ```
2. 根据镜像生成运行新容器
   ```bash
   $ docker run -id --name=mynginx2 -p 81:80 mynginx_i
   ```
3. 镜像导出为一个文件
   ```bash
   $ docker save -o mynginx.tar mynginx_i
   ```
4. 文件恢复成镜像
   ```bash
   $ docker load -i mynginx.tar
   ```

### 删除镜像
需要先删除此镜像生成的容器
1. 停止容器
2. 删除容器
3. 删除镜像
```bash
   $ docker stop 容器名
   $ docker rm 容器名
   $ docker rmi 镜像名
```
```bash 
$ docker ps -a //查看所有容器
$ docker images //查看所有镜像
```
---

## Dockerfile
相当于一个构建自定义镜像的脚本
> [dockerfile官方文档说明 ](https://docs.docker.com/engine/reference/builder/)

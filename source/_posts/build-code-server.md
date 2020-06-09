---
title: docker-componse搭建code-server服务
date: 2020-03-04 00:00:00
tags: 
    - docker-componse 
    - vscode
---

> code-server is vscode running on a remote server, accessibale throught the browser.
> code-server就是一个可以搭建在服务器上的远程vscode，并且我们可以直接用browser直接访问,本来的初衷是为了写blog可以直接在浏览器中写，而不是每次必须打开vscode，后来发现code-server也可以用在ipad上，兼容性还不错
> 参考自：**https://github.com/cdr/code-server**

## X00
使用docker搭建code-server的优势是搭建速度比较快，但是没有办法使用宿主机的环境和配置，如果需要配置需要从头开始配置。

Docker Compose 是 Docker 官方编排（Orchestration）项目之一，负责快速的部署分布式应用。使用docker compose我们的docker命令可以复用，也可以构成更高级的配置文件。

## 配置

新建文件夹`mkdir /usr/local/code-server` 可以在任意位置，用来存放建立的新工程，打开新建的文件夹`cd /usr/local/code-server`，新建并编辑docker-compose`touch docker-compose.yml && vim docker-compose.yml`

```makefile
version: "3"

services:
  code-server:
    container_name: code-server
    image: codercom/code-server
    ports:
      - "8080:8080"
    volumes:
      - "/usr/local/code-server/project:/home/coder/project"
    environment:
      PASSWORD: <password>
    restart: always
```

- ports
    前面为宿主机端口可以改为需要的端口，后面为容器内端口不能更改
- volumes
    数据卷：前面为新建的文件夹 + 'project'
- restart
    容器意外关闭后可以自动重启

## 启动容器
```bash
#在cocker-compose.yml目录下

docker-compose up ##正常启动
docker-compose up -d ##后台启动

# kill docker container
docker ps #查看对应的code-server id
docker kill <id>
```

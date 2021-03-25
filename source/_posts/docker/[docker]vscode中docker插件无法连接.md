---
title: vscode中docker插件无法连接
date: 2021-03-06
tags:
    - docker
    - vscode
---
# vscode中docker插件无法连接
**报错Failed to connect. Is Docker running**
**Error: connect EACCES /var/run/docker.sock**

## 原因分析
原因是docker使用unix socket进行通讯，但是unix socket属于root用户，但是普通用户需要使用sudo才能开启root权限，但是普通的操作并没有root权限。

## 解决方案
1. 使用root用户登录，但是安全性没有保障
2. 普通用户增加到docker组中
    ```
    sudo groupadd docker          #添加docker用户组
    sudo gpasswd -a $USER docker  #将当前用户添加至docker用户组
    newgrp docker                 #更新docker用户组
    ```
添加后重新登录vscode，用户重新连接后docker插件会正常运行。

## 测试
可以运行`docker ps`命令，如果有正常的输出，则正常。
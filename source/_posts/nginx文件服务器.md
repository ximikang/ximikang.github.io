---
title: windows下nginx搭建文件服务器附带基本验证功能
date: 2020-10-29 00:00:00
tags: 
    - nginx
    - 服务器
---

# windows下nginx搭建文件服务器附带基本验证功能
## 配置nginx
在nginx的配置文件中添加如下配置文件
```
server {
    listen       7777 default_server;
    listen       [::]:7777 default_server;

    location / {
        # 文件目录
        alias D:/share;
        # 基本验证
        auth_basic "nginx basic auth";
        auth_basic_user_file C:/nginx/conf/htpasswd;
        # 文件显示功能
        autoindex on;    #开启索引功能
        autoindex_exact_size off;  #关闭计算文件确切大小（单位bytes），只显示大概大小（单位kb、mb、gb）
        autoindex_localtime on;   #显示本机时间而非 GMT 时间
    }
}
```
## 新建用户文件
在C:/nginx/conf中新建htpasswd文件,按照如下的排列方式新建用户
```
username:password
```

## reload nginx
```shell
nginx.exe -s reload
```
在网站上输入账户和密码即可以访问文件服务


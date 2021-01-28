---
title: nextcloud配置优化和常见问题(FAQ)
date: 2018-10-29 00:00:00
tags: 
    - nextcloud
    - 服务器
    - FAQ
---

# FAQ：
1. **设置cron后台任务**
使用nginx用户进行定时任务启动(为了优化性能, 正确配置后台任务非常重要. 对于较大的实例, 推荐配置为 'Cron'. 详情请参考相关文档.)

```shell
vim   /etc/crontab

SHELL=/bin/bash
PATH=/sbin:/bin:/usr/sbin:/usr/bin
MAILTO=root

# For details see man 4 crontabs

# Example of job definition:
# .---------------- minute (0 - 59)
# |  .------------- hour (0 - 23)
# |  |  .---------- day of month (1 - 31)
# |  |  |  .------- month (1 - 12) OR jan,feb,mar,apr ...
# |  |  |  |  .---- day of week (0 - 6) (Sunday=0 or 7) OR sun,mon,tue,wed,thu,fri,sat
# |  |  |  |  |
# *  *  *  *  * user-name  command to be executed
*/15  *  *  *  * nginx php -f /var/www/nextcloud/cron.php

```
重启crond
> systemctl restart crond

2. **日志出现错误ERROR:you are using a fallback implementation of the intl extension**
由于没有安装init模块

```shell
yum install php7.1w-init
systemctl restart php-fpm
systemctl restart nginx
```
需要注意的是php应该对应自已的版本，nginx对应的是自己的服务器

3. **提示没有配置好opcache.ini**

```shell
vim /etc/php.d/opcache.ini
修改/etc/php.d/opcache.ini，将以下行注释去掉，并修改为对应的配置值
zend_extension=opcache.so
opcache.enable=1
opcache.enable_cli=1
opcache.memory_consumption=128
opcache.interned_strings_buffer=8
opcache.max_accelerated_files=10000
opcache.revalidate_freq=1
opcache.save_comments=1
```
4. **登陆无限启动的问题**
首先检查php-fpm最顶端的设置 user和group，`/etc/php-fpm.d/www.conf`
设置php的session的储存目录的所有者

```shell
chown nginx:nginx /var/lib/php/session/
```
实在没办法的话，可以试试

```shell
chmod -R 777 /var/lib/php/session/
```
最后一定要记得清除浏览器的cookies

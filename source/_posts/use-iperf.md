---
title: 使用iperf进行内网测速
tags: 服务器
date: 2020-07-18 10:32:41
---


一般的测速网站或者是常用的测速软件如speedtest通常都只能测试外网的连接的速度，但是对于在局域网内部的设备之间测速是没有办法解决的。

iperf 可以测试网络的性能，并且方便搭建，只需要命令行就可以快速对局域网进行网络性能的测试。


# 下载和安装

下载网站：[iperf download](https://iperf.fr/iperf-download.php)
- [windows](https://iperf.fr/iperf-download.php#windows)
- [ubuntu](https://iperf.fr/iperf-download.php#ubuntu)
解压就可以运行

# 最简单的使用方式
再windows上打开下载的文件夹
将一台电脑作为服务端：

``` bash
iperf3 -s
# windows
iperf3.exe -s
```
另外一台电脑作为客户端：
```
iperf3 -c 服务器ip
```

## 测试
服务器端新建服务器

![](https://ximikang-ciu.oss-cn-beijing.aliyuncs.com/img/20200718102943.png?x-oss-process=style/common)
客户端新建连接进行测速
![](https://ximikang-ciu.oss-cn-beijing.aliyuncs.com/img/20200718102646.png?x-oss-process=style/common)

需要注意的是在服务器端需要开启5201端口或设置其他端口，或者关闭防火墙。
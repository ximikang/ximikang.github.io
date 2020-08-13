---
title: vmware使用主机代理
tags:
    - vmware
    - proxy
date: 2020-08-13
---
## 主机配置
主机的配置主要为了虚拟机可以使用主机的网络。
### 代理配置
通常使用ssr作为科学上网的工具，如果需要虚拟机使用代理，则需要打开ssr设置中的本地代理，并允许局域网的连接。
这样通过此端口的流量就可以通过ssr，流量的pac和代理规则也交付个ssr进行控制。
一般配置代理端口为1080端口

![](https://ximikang-ciu.oss-cn-beijing.aliyuncs.com/img/20200813113025.png)
### vmware 网络配置
vmware虚拟机使用Nat模式，这样相当于主机为一个路由器，可以给每个虚拟机分配ip地址。访问主机也就是访问网络的网关。

![](https://ximikang-ciu.oss-cn-beijing.aliyuncs.com/img/20200813113439.png)
## ubuntu配置
主要配置浏览器的访问和终端的访问
### 浏览器配置
通过设置`setting-> network -> network Proxy`
设置为Manual模式,使用http或socks代理，地址为主机的地址可以通过主机的ipconfig查看，一般为VMnet8网卡的地址。

![](https://ximikang-ciu.oss-cn-beijing.aliyuncs.com/img/20200813112643.png)

端口号为主机设置的对应端口

![](https://ximikang-ciu.oss-cn-beijing.aliyuncs.com/img/20200813112218.png)

### 终端配置
以上的代理配置仅限于浏览器的使用，对于终端的程序是没有用的。
通常的终端应用都可以通过自身的proxy代理配置，比如git, wget, apt.但是每个应用都得重新配置一次，为了方便的快捷的使用代理，在终端可以借助`proxychains`工具进行方便的代理访问。
> https://github.com/haad/proxychains

在ubuntu中可以使用apt安装proxychains

```shell
sudo apt install proxychains
```
通过 **/etc/proxychains.conf** 配置代理
```shell
# ProxyList format
#       type  host  port [user pass]
#       (values separated by 'tab' or 'blank')
#
#
#        Examples:
#
#               socks5  192.168.67.78   1080    lamer   secret
#               http    192.168.89.3    8080    justu   hidden
#               socks4  192.168.1.49    1080
#               http    192.168.39.93   8080    
#               
#
#       proxy types: http, socks4, socks5
#        ( auth types supported: "basic"-http  "user/pass"-socks )
#
[ProxyList]
# add proxy here ...
# meanwile
# defaults set to "tor"
# 在最后添加配置文件
http 192.168.164.1 1080
socks5 192.168.1.123 1080
```
可能会出现找不到libproxychains.so.3
```
$ find /usr -name "libproxychains.so.3"
/usr/lib/x86_64-linux-gnu/libproxychains.so.3
```
将对应的路径添加到/usr/bin/proxychains中的LD_PRELOAD中。
#### usage example
```
sudo proxychains apt update

#proxychains ping google.com
# 这条命令不会起作用，因为proxychains只能代理TCP连接，ping命令通过ICMP协议作用
```


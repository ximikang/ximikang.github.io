---
title: ubuntu18.04 安装librealsense并验证
tags:
  - realsense
date: 2020-08-12 00:00:00
update: 
---

## 安装环境
`OS: Ubuntu 18.04 bionic`
`Kernel: x86_64 Linux 4.15.0-20-generic`

`测试通过了vmware station开启的虚拟机ubuntu18.04`

## 安装Realsense SDK
> 参考https://github.com/IntelRealSense/librealsense/blob/master/doc/distribution_linux.md

以前的Librealsense需要编译进行安装,但是对于Ubuntu LTS kernels 4.4, 4.8, 4.10, 4.13, 4.15, 4.18* 5.0* 5.3*可以使用apt 直接进行安装。

建议使用科学上网进行安装，否则会下载很慢

```shell
# 注册public key
sudo apt-key adv --keyserver keys.gnupg.net --recv-key F6E65AC044F831AC80A06380C8B3A55A6F3EFCDE || sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-key F6E65AC044F831AC80A06380C8B3A55A6F3EFCDE

# 添加apt repository
sudo add-apt-repository "deb http://realsense-hw-public.s3.amazonaws.com/Debian/apt-repo bionic main" -u
# ubuntu 16可以换为
# sudo add-apt-repository "deb http://realsense-hw-public.s3.amazonaws.com/Debian/apt-repo xenial main" -u

# 安装librealsense
sudo apt-get install librealsense2-dkms
sudo apt-get install librealsense2-utils
```

## 验证是否安装成功
重新连接RealSense相机并运行`realsense-viewer`去验证是否安装成功
![](https://ximikang-ciu.oss-cn-beijing.aliyuncs.com/img/20200812194410.png?x-oss-process=style/common)
## 升级librealsense
```shell
sudo apt-get update
sudo apt-get upgrade
```

## 卸载librealsense
```shell
# 卸载所有的realsense相关的安装包
dpkg -l | grep "realsense" | cut -d " " -f 3 | xargs sudo dpkg --purge
```

## 相关安装包的内容和依赖
Name    |      Content   | Depends on |
-------- | ------------ | ---------------- |
librealsense2-udev-rules | Configures RealSense device permissions on kernel level  | -
librealsense2-dkms | DKMS package for Depth cameras-specific kernel extensions | librealsense2-udev-rules
librealsense2 | RealSense™ SDK runtime (.so) and configuration files | librealsense2-udev-rules
librealsense2-utils | Demos and tools available as a part of RealSense™ SDK | librealsense2
librealsense2-dev | Header files and symbolic link for developers | librealsense2
librealsense2-dbg | Debug symbols for developers  | librealsense2

可以按照需求进行安装
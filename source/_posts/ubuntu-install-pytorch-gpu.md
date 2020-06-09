---
title: ubuntu搭建GPU pytorch版本
date: 2019-11-11 00:00:00
tags: 
  - pytorch 
  - ubuntu
---

## 安装显卡驱动

### 禁止集成的nouveau驱动

```bash
vim /etc/modprobe.d/blacklist.conf

#添加几行
blacklist vga16fb
blacklist nouveau
blacklist rivafb
blacklist rivatv
blacklist nvidiafb

#执行
update-initramfs -u
reboot

#重启后执行
lsmod | grep nouveau

#没有显示说明已经禁用
```

### 开始安装

```bash
#安装必要
apt-get update
apt-get install gcc g++ make -y

#按照提示进行安装
bash NVIDIA-Linux-x86_64-440.31.run

#安装后执行，可以查看gpu状态
nvidia-smi 

#查看gpu的驱动版本
cat /proc/driver/nvidia/version
```

## Anaconda

- 下载Anaconda, 如果在国内的话建议使用国内的mirror下载， https://mirrors.tuna.tsinghua.edu.cn/anaconda/archive/ 清华镜像源的镜像，找到对应的版本进行下载.
  
    ```bash
    wget https://mirrors.tuna.tsinghua.edu.cn/anaconda/archive/Anaconda3-5.3.1-Linux-x86_64.sh
    bash Anaconda3-5.3.1-Linux-x86_64.sh
    ```

- 按照提示进行安装
  
- 生效环境变量
  
  ```bash
  source ~/.bashrc
  ```

- 换清华镜像源
  
  ```bash
    conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
    conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/
    conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/pytorch/
    conda config --set show_channel_urls yes
  ```

- 创建一个name为pytorh python版本为3.7的虚拟环境。
  
  ```bash
    conda create -n pytorch python=3.7
    #切入环境
    source activate pytorch
  ```

## pytroch
配置好conda环境后就，pytorch就可以顺利安装了。

**此处不能按照官方进行安装conda install pytorch torchvision cudatoolkit=10.1 -c pytorch**

```bash
#需要去掉-c torch后，可以从清华镜像快速下载。
conda install pytorch torchvision cudatoolkit=10.1

```
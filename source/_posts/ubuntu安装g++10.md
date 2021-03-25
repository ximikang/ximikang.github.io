---
title: ubuntu18.04安装g++10
date: 2021-03-21 00:00:00
tags:
    - ubuntu
    - g++
---

由于想使用c++20中最新的协程特性,这应该是最简单的安装方式，避免了编译的g++。
```shell
# 添加ppa库
sudo add-apt-repository ppa:ubuntu-toolchain-r/test

# 安装g++
sudo apt install g++-10
```
---
title: ubuntu系统调节GPU风扇转速
tags:
    - ubuntu
    - GPU
    - 风扇
    - fan
date: 2021-3-9
---
# ubuntu系统调节GPU风扇转速
## 查看NVIDIA GPU温度和转速
```shell
nvidia-smi
```

![](https://ximikang-ciu.oss-cn-beijing.aliyuncs.com/img/20210309214845.png?x-oss-process=style/common)



## 有桌面的ubuntu
可以通过`nvidia-settings`进项图像化界面的设置。
## 无桌面ubuntu[headless linux server]

### 使用coolgpus脚本进行调节
> https://github.com/andyljones/coolgpus
安装：
使用pypi进行安装
```shell
pip install coolgpus
```
使用用例：

```shell
# 将gpu风扇转速设置为99%
sudo $(which coolgpus) --speed 99 99

# 关闭设置
sudo $(which coolgpus)

# 或者也可以设置线性控制
# 这个模式下20℃以下转速为5%， 20-55℃之间转速为30%，依次类推
sudo $(which coolgpus) --temp 20 55 80 --speed 5 30 99
```

如果需要将coolgpus脚本当作一个系统服务长期运行的话，如果你的服务器采用systemd管理server的话，可以在`/etc/systemd/system/coolgpus.service`创建模板
```config
[Unit]
Description=Headless GPU Fan Control
After=syslog.target

[Service]
ExecStart=/home/ajones/conda/bin/coolgpus --kill 
Restart=on-failure
RestartSec=5s
ExecStop=/bin/kill -2 $MAINPID
KillMode=none 

[Install]
WantedBy=multi-user.target
```

可以通过如下命令进行控制
```shell
sudo systemctl enable coolgpus
sudo systemctl start coolgpus
```

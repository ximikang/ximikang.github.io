---
title: nodejs搭建mqtt服务器(Broker)
date: 2020-12-16 00:00:00
tags: 
    - nodejs
    - iot
    - aedes
    - mqtt.js
---
# MQTT通讯协议
MQTT(消息队列遥测传输)是一种开放式OASIS和ISO标准的轻量级发布订阅网络协议，可在设备之间传输消息，是当下使用广泛的物联网通信协议之一。

IBM公司开发了MQTT协议的第一个版本, 设计思想是轻巧、开放、简单、规范，易于实现。
这些特点使得它对很多场景来说都是很好的选择，特别是对于受限的环境如机器与机器的通信（M2M）以及物联网环境（IoT）。
![](https://ximikang-ciu.oss-cn-beijing.aliyuncs.com/img/20201218003133.png)
图中包括了MQTT协议中的两个角色和基本的订阅和发布模式

- broker/client

    broker为代理端也就是我们要搭建的服务器，client也就是每个终端温度传感器或者是电脑笔记本等
- 发布/订阅

    手机或者电脑client向broker订阅一个temp topic，当温度传感器client发布一个temp topic则手机或者电脑同时可以接收到订阅的内容

# 搭建mqtt Broker
mqtt Broker使用aedes进行搭建
```js
const aedes = require('aedes')()
const server = require('net').createServer(aedes.handle)
const port = 18080

server.listen(port, function () {
    console.log('server started and listening on port ', port)
})
```
# 测试mqtt Broker
搭建两个客户端进行模拟，每隔一秒发送一次temp。客户端使用mqtt.js
1. client_pub.js
```js
var mqtt = require("mqtt")
var client = mqtt.connect("mqtt://localhost:18080")

// 连接后不断发布temp topic
client.on("connect", (e) => {
    console.log("success connect mqtt server");
    setInterval(() => {
        client.publish("temp", "25.6")
        console.log("send it")
    }, 1000)
})
```
2. client_sub.js
```js
var mqtt = require("mqtt")
var client = mqtt.connect("mqtt://localhost:18080")

// 连接后订阅temp topic
client.on('connect', (e) => {
  console.log("success connect mqtt server");
  client.subscribe('temp', function (err) {
    console.log("subscribe temp topic")
  })
})

// 监听订阅的message
client.on('message', function (topic, message) {
  // message is Buffer
  console.log(topic + ":\t" + message.toString())
})
```

启动服务后运行两个客户端得到以下结果：
1. 服务器
```shell
server started and listening on port  18080
```
2. 客户端
```shell
# node mqtt-pub.js 
success connect mqtt server
send it
send it
send it
send it
send it
send it
send it
send it
send it
send it
send it

# node mqtt-sub.js 
success connect mqtt server
subscribe temp topic
temp:   25.6
temp:   25.6
temp:   25.6
temp:   25.6
temp:   25.6
temp:   25.6
temp:   25.6
temp:   25.6
```
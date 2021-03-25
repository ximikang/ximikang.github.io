---
title: Vue build后快速测试发布
tags: vue
date: 2020-06-28
---

# Vue build后快速测试发布
> 最近新建了一个vue的小项目，需要再服务器上测试，但是又没有必要配置CI。
> 所以在

---

## 安装scp2
> A pure javascript secure copy program based on ssh2.scp2 is greatly powered by ssh2, implemented the scp in a sftp way.It is written in pure javascript, and should work on every OS, even Windows. Nodejs (v0.8.7 or newer) is required to make it work.

`npm install scp2 --save`

## 使用scp2将build的文件上传到服务器

在项目根目录新建deploy.js文件
```javascript
// ./deploy.js
const scpClient = require('scp2');

scpClient.scp('dist/',
            {host: hostname,
            port: port,
            username: username,
            password: password,
            path: yourpath},
            (err) => {
                if(err){
                    console.log("发布失败");
                    throw err;
                } else {
                    console.log("成功发布")
                }
            })
```

也可以在package.json中建立新的脚本
`"deploy": "npm run build & node deploy.js"`
就可以使用npm run deploy将工程build并上传
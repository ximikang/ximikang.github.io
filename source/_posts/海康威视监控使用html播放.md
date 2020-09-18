---
title: 海康威视监控使用html播放
date: 2020-09-17 00:00:00
tags: 
    - html
    - 海康威视
    - hls
    - rtsp
---

## 存在的问题
- rtsp
    无法直接在网页端播放，需要插件的支持。不考虑。
- rtmp
    可以在网页播放，但是播放器需要flash的支持，chrome在2020年后对flash的支持十分不友好,但是延迟比较底。
- hls
    可以在网页播放，也不需要插件和flash的支持，但是缺点在于hls将网页进行切片传输，每次切片都会造成时间的延迟，而且在网络不好的地方，视频会有克顿和无法播放的现象。

**本文使用ffmpeg对网络镜头的视频流进行转码为hls，通过nginx进行代理，网页端通过hls.js显示网络镜头的画面。**

## ffmpeg转码为hls
通过ffmpeg将rtsp转码为hls，转码的输入为网络摄像机的子码流，视频和音频的解码直接使用镜头的解码编码器h264和acc。-hls_time 为 hls切片的时间间隔，时间越短则延迟越低，但是传输的带宽越大对服务器要求更高，-hls_list_size为.m3u8文件的长度，-hls_wrap为本地交换文件的大小。
```shell
start ffmpeg ^
-i rtsp://username:password@192.168.1.123/Streaming/Channels/102?transportmode=unicast  ^
-c copy ^
-f   hls  ^
-hls_time 1.0 ^
-hls_list_size 2 ^
-hls_wrap   2  ^
 C:/nginx-1.19.2/html/hls/test.m3u8
```
## 搭建nginx并配置
nginx.conf配置文件
```

#user  nobody;
worker_processes  1;

#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#pid        logs/nginx.pid;


events {
    worker_connections  1024;
}


http {
    include       mime.types;
    default_type  application/octet-stream;
    # 跨域访问
    add_header Access-Control-Allow-Origin *;
    add_header Access-Control-Allow-Headers X-Requested-With;
    add_header Access-Control-Allow-Methods GET,POST,OPTIONS;


    #access_log  logs/access.log  main;

    sendfile        on;
    keepalive_timeout  65;

    server {
        listen       80;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
            root   html;
            index  index.html index.htm;
        }
}

```

## 前端视频显示
前端为了能够播放hls, 使用hls.js播放直播视频，hls.js不需要任何的播放器，可以直接在video元素上运行
> https://www.npmjs.com/package/hls.js/v/canary

- html
```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>hls test</title>
</head>

<body>

    <h1>hls test</h1>
    <script src="https://cdn.jsdelivr.net/npm/hls.js@latest"></script>
    <video id="video"></video>
    <script>
        var video = document.getElementById('video');
        // 视频的路径
        var videoSrc = 'http://localhost/hls/test.m3u8';
        if (Hls.isSupported()) {
            var hls = new Hls();
            hls.loadSource(videoSrc);
            hls.attachMedia(video);
            hls.on(Hls.Events.MANIFEST_PARSED, function () {
                video.play();
            });
        }
        else if (video.canPlayType('application/vnd.apple.mpegurl')) {
            video.src = videoSrc;
            video.addEventListener('loadedmetadata', function () {
                video.play();
            });
        }
    </script>
</body>

</html>
```

- vue
```html
<template>
    <div>
        <video ref="videoRef" height="500px" autoplay />
    </div>
</template>

<script>
import Hls from 'hls.js'
export default {
    data() {
        return {
            hls:''
        }
    },
    mounted: function() {
        const videoRef = this.$refs.videoRef
        const url = 'http://localhost/hls/test.m3u8'
        this.hls = new Hls();
        this.hls.loadSource(url)
        this.hls.attachMedia(this.$refs.videoRef)
        this.hls.on(Hls.Events.MANIFEST_PARSED, () => {
            console.log('加载成功');
            this.$refs.video.play();
          });
        this.hls.on(Hls.Events.ERROR, (event, data) => {
            console.log('加载失败');
        });
    }
}
</script>
```

## 最终显示效果
![](https://ximikang-ciu.oss-cn-beijing.aliyuncs.com/img/20200916173720.png)

视频的延迟大概有3-4秒，对于实时性较高的应用还是达不到要求。
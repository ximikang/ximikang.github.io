---
title: Git 使用代理加速
tags: git
---
在国内使用github最近一直都是20kb,太折腾人了. 想到使用代理加速git

```
#use proxy
git config --global http.proxy 'socks5://127.0.0.1:1080'

git config --global https.proxy 'socks5://127.0.0.1:1080'

#unuse proxy
git config --global --unset http.proxy

git config --global --unset https.proxy
```
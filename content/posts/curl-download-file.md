---
title: "在服务器中CURL命令下载文件"
date: 2019-12-08
draft: false
tags: ["curl"]
categories: ["服务器"]
---

在下载`https://nx318.your-next.cloud/s/x56jafH96wwAYrG/download`这个地址的文件时候，如果在浏览器正常打开下载的话，应该是`open-semantic-desktop-search_19.07.19.ova`这个文件名。然鹅。。在使用
```linux
curl -O https://nx318.your-next.cloud/s/x56jafH96wwAYrG/download
```
下载的时候结果文件名变成了`download`
   
?????黑人问号脸.jpg

查阅了一下如果加上-J 参数的话就对了

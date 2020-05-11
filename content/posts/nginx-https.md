---
title: "Nginx配置域名https"
date: 2020-01-01T14:48:52+08:00
draft: false
tags: ["nginx"]
categories: ["服务器"]
---


```nginx

server {
        listen              443 ssl;
        server_name         www.example.com;
        
        #设置长连接
        keepalive_timeout   70;
        
        #HSTS策略
        add_header Strict-Transport-Security "max-age=31536000; includeSubDomains; preload" always;
        
        #证书文件
        ssl_certificate     www.example.com.crt;
        #私钥文件
        ssl_certificate_key www.example.com.key; 
        
        #优先采取服务器算法
        ssl_prefer_server_ciphers on;
        #使用DH文件
        ssl_dhparam /etc/ssl/certs/dhparam.pem;
        ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
        #定义算法
        ssl_ciphers "EECDH+ECDSA+AESGCM EECDH+aRSA+AESGCM EECDH+ECDSA+SHA384 EECDH+ECDSA+SHA256 EECDH+aRSA+SHA384 EECDH+aRSA+SHA256 EECDH+aRSA+RC4 EECDH EDH+aRSA !aNULL !eNULL !LOW !3DES !MD5 !EXP !PSK !SRP !DSS !RC4";
        #减少点击劫持
        add_header X-Frame-Options DENY;
        #禁止服务器自动解析资源类型
        add_header X-Content-Type-Options nosniff;
        #防XSS攻擊
        add_header X-Xss-Protection 1;
 }
 ```

---
JS错误:Mixed Content: The page at `https://XXX` was loaded over HTTPS, but requested an insecure

**原因是** ：

> 一个https的网站中，混合着http请求

**解决办法**：

> 在nginx  server添加  add_header Content-Security-Policy upgrade-insecure-requests;
    
>解决https请求http资源不可用的情况

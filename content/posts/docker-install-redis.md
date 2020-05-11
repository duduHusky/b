---
title: "docker安装redis"
date: 2019-12-11
draft: false
tags: ["redis", "docker"]
categories: ["通用"]
---

# 拉取Docker上的Redis
```linux
docker pull reids
```

# 启动镜像
docker run -p 6379:6379 --name myredis -v /usr/local/docker/redis/redis.conf:/etc/redis/redis.conf  -v /usr/local/docker/redis/data:/data --privileged=true -d redis redis-server /etc/redis/redis.conf --appendonly yes

|参数|说明|
| ---- | ---- |
| -p 6379:6379 |宿主机端口映射docker端口
|--name myredis |给启动的容器命个名字|
|-v /path/redis.conf:/etc/redis/redis.conf| 宿主机redis配置文件映射docker配置文件|
|-v /path/data:/data | 宿主机data目录映射docker的data目录|
|--privileged=true | 容器内的root拥有真正root权限，否则容器内root只是外部普通用户权限|
| -d redis |本地运行下载的redis镜像|
| redis-server /etc/redis/redis.conf | redis启动命令|
| --appendonly yes | 开启持久化|


> redis.conf 文件可以在官网下载 [redis.conf](http://download.redis.io/redis-stable/redis.conf)
>
> !!!注意: redis.conf 文件修改一定一定要注释 `daemonize yes` 这一行,如果为YES 会导致 redis 无法启动，因为后台会导致docker无任务可做而退出
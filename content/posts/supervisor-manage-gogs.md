---
title: "使用Supervisor管理gogs"
date: 2020-01-23T14:47:09+08:00
draft: false
tags: ["gogs"]
categories: ["服务器"]
---

启动`supervisord`命令后
查看管理进程运行状态`supervisorctl status`
发现出现问题:
```linux
2019-10-28 11:21:33,510 INFO supervisord started with pid 6613
2019-10-28 11:21:34,513 INFO spawned: 'gogs' with pid 6615
2019-10-28 11:21:34,517 INFO exited: gogs (exit status 127; not expected)
2019-10-28 11:21:35,520 INFO spawned: 'gogs' with pid 6617
2019-10-28 11:21:35,524 INFO exited: gogs (exit status 127; not expected)
2019-10-28 11:21:37,528 INFO spawned: 'gogs' with pid 6619
2019-10-28 11:21:37,532 INFO exited: gogs (exit status 127; not expected)
2019-10-28 11:21:40,537 INFO spawned: 'gogs' with pid 6623
2019-10-28 11:21:40,543 INFO exited: gogs (exit status 127; not expected)
2019-10-28 11:21:41,544 INFO gave up: gogs entered FATAL state, too many start retries too quickly
```
搜索后找到方法：
去掉`gogs.conf`中的`directory=/xxx/xxx` 配置即可

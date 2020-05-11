---
title: "Centos7安装docker"
date: 2019-12-09
draft: false
tags: ["docker"]
categories: ["服务器"]
---

## 设置yum源
`yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo`

> 这一步如果提示 `bash: yum-config-manager not found` 
> 是因为系统默认没有安装这个命令，这个命令在yum-utils 包里，可以通过命令`yum -y install yum-utils` 安装就可以了

## 可以查看所有仓库中所有docker版本，并选择特定版本安装
`yum list docker-ce --showduplicates | sort -r`

## 安装docker
`yum install docker-ce`
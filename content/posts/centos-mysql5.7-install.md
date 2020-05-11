---
title: "Centos7安装Mysql5.7以上"
date: 2019-12-10
draft: false
tags: ["mysql"]
categories: ["服务器","Mysql"]
---

1. 卸载原系统中的mariadb……
首先执行命令rpm -qa|grep mariadb查看是否有mariadb的安装包，没有可以无视
接下来，执行 rpm -e --nodeps mariadb-libs删除它


2.  下载mysql5.7安装包
1.前往官方网站复制yum源链接[Mysql官网](https://dev.mysql.com/downloads/repo/yum/)
执行wget https://dev.mysql.com/get/mysql80-community-release-el7-1.noarch.rpm（即你复制的下载链接）进行下载

3. 进行yum源安装
执行rpm -ivh mysql80-community-release-el7-1.noarch.rpm命令
接下来可以通过yum repolist all | grep mysql查看yum源中的mysql安装包

4. 进行mysql安装
`可以看到yum源中默认启用的安装包版本为MySQL8.0，如果需要切换为5.7，需要运行以下命令`

    ```linux
    yum-config-manager --disable mysql80-community
    yum-config-manager --enable mysql57-community
    ```

    > 注意：yum-config-manager默认使用的是python2版本环境，如果用户使用这个命令时报错，可能是因为当前默认python版本为python3版本。需要在/bin/yum-config-manager文件中修改文件头#!/usr/bin/python -tt为#!/usr/bin/python2 -tt

    接下来可以开始进行安装步骤，执行命令
    `yum install mysql-community-server`
    进行安装，需要依赖安装时选择y就ok

5.  启动mysql服务
执行命令systemctl start mysqld.service来启动mysql服务，systemctl status mysqld.service可查看mysql服务运行状态
MySQL服务器初始化（从MySQL 5.7开始）：在服务器初始启动时，如果服务器的数据目录为空，则会发生以下情况：

    + 服务器已初始化。

    + 在数据目录中生成SSL证书和密钥文件。

    + 该validate_password插件安装并启用。

    + 将'root'@'localhost' 创建一个超级用户帐户。设置超级用户的密码并将其存储在错误日志文件中。要显示它，请使用以下命令：
    `grep 'temporary password' /var/log/mysqld.log`

通过使用生成的临时密码登录并为超级用户帐户设置自定义密码，尽快更改root密码：

`mysql -u root -p`
输入以上临时密码
`ALTER USER 'root'@'localhost' IDENTIFIED BY '您的密码';`

> 注意
MySQL的 validate_password 插件默认安装。这将要求密码包含至少一个大写字母，一个小写字母，一个数字和一个特殊字符，并且密码总长度至少为8个字符。


如果您需要修改简单密码，可以依次执行以下操作步骤:

```mysql
set global validate_password_policy=0;
set global validate_password_length=1;
set global validate_password_mixed_case_count=2;

-- 然后进行密码更改
ALTER USER 'root'@'localhost' IDENTIFIED BY '123456';
```

这时候MySQL基本上就配置完成了
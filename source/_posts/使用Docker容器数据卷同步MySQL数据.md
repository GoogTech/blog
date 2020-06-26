---
title: 使用Docker容器数据卷同步MySQL数据
date: 2020-06-26 00:11:06
tags: [Docker]
---

### 1. 拉取 MySQL 镜像
```shell
# ok
```

### 2.运行 MySQL 容器
```shell
# ok
# 查看当前正在运行的容器(无)
ubuntu@VM-0-4-ubuntu:~$ sudo docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES

# 以后台启动的方式运行 mysql 容器，并设置数据卷来同步容器中的 mysql 数据
# --name="" : 指定容器名字，后续可以通过名字进行容器管理，links特性需要使用名字
# -e, --env=[] : 指定环境变量，容器中可以使用该环境变量
# -i, --interactive=false : 以交互模式运行容器，通常与 -t 同时使用
# -t, --tty=false : 为容器重新分配一个伪输入终端，通常与 -i 同时使用
# -d, --detach=false : 后台运行容器，并返回容器ID
# -p, --publish=[] : 指定容器暴露的端口
# -v, --volume=[] : 给容器挂载存储卷，挂载到容器的某个目录
ubuntu@VM-0-4-ubuntu:~$ sudo docker run --name mysql5.7.0 -e MYSQL_ROOT_PASSWORD=loveyourself -it -d -p 3399:3306 -v / home/mysql/conf:/etc/mysql/conf.d -v /home/mysql/data:/var/lib/mysql mysql:5.7 /bin/bash
b434f046aaee77451866278eaff1f4e23ba372ff74d83659845d83cb107c01d2

# 再次查看当前正在运行的容器，验证 mysql 容器是否启动成功
ubuntu@VM-0-4-ubuntu:~$ sudo docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                               NAMES
b434f046aaee        mysql:5.7           "docker-entrypoint.s…"   3 seconds ago       Up 2 seconds        33060/tcp, 0.0.0.0:3399->3306/tcp   mysql5.7.0
```

### 3. 连接 MySQL
```shell
# fail 
# 在此之前尝试过使用 MySQL8.0.22，但连接时也抛出如下错误信息
ubuntu@VM-0-4-ubuntu:~$ sudo docker exec -it mysql5.7.0 /bin/bash
root@b434f046aaee:/# mysql -u root -p
Enter password: # input the pwd : loveyourself
ERROR 2002 (HY000): Can't connect to local MySQL server through socket '/var/run/mysqld/mysqld.sock' (2)
```

> 注 : 若执行 `sudo docker run --name mysql5.7.0 -e MYSQL_ROOT_PASSWORD=loveyourself -it -d -p 3399:3306 -v / home/mysql/conf:/etc/mysql/conf.d -v /home/mysql/data:/var/lib/mysql mysql:5.7` 则会发现该容器启动不一会儿就会停止运行，其问题分析及无法连接 MySQL 的解决方法请参考文章 : [Docker安装MySQL及基本使用](https://ishacker.net/2020/06/24/Docker%E5%AE%89%E8%A3%85MySQL%E5%8F%8A%E5%9F%BA%E6%9C%AC%E4%BD%BF%E7%94%A8/)

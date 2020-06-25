---
title: Docker安装MySQL及基本使用
date: 2020-06-24 19:04:15
tags: [Docker,MySQL]
---


### 1.搜索 MySQL 镜像
```shell
ubuntu@VM-0-4-ubuntu:~$ sudo docker search mysql
NAME                              DESCRIPTION                                     STARS               OFFICIAL              AUTOMATED
mysql                             MySQL is a widely used, open-source relation…   9673                [OK]
mariadb                           MariaDB is a community-developed fork of MyS…   3518                [OK]
mysql/mysql-server                Optimized MySQL Server Docker images. Create…   705                 [OK]
```


### 2.拉取 MySQL 镜像
```shell
# 拉取版本为 8.0.20 的 MySQL 镜像 
ubuntu@VM-0-4-ubuntu:~$ sudo docker pull mysql:8.0.20
8.0.20: Pulling from library/mysql
Digest: sha256:8b7b328a7ff6de46ef96bcf83af048cb00a1c86282bfca0cb119c84568b4caf6
Status: Downloaded newer image for mysql:8.0.20
docker.io/library/mysql:8.0.20

# 查看 MySQL 镜像是否拉取成功
ubuntu@VM-0-4-ubuntu:~$ sudo docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
ubuntu              latest              74435f89ab78        8 days ago          73.9MB
ubuntu              18.04               8e4ce0a6ce69        8 days ago          64.2MB
tomcat              9.0                 2eb5a120304e        2 weeks ago         647MB
tomcat              latest              2eb5a120304e        2 weeks ago         647MB
nginx               latest              2622e6cca7eb        2 weeks ago         132MB
mysql               8.0.20              be0dbf01a0f3        2 weeks ago         541MB
mysql               latest              be0dbf01a0f3        2 weeks ago         541MB
hello-world         latest              bf756fb1ae65        5 months ago        13.3kB
```


### 3.运行 MySQL 容器
```shell
# 查看当前正在运行容器(无)
ubuntu@VM-0-4-ubuntu:~$ sudo docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES

# 查看本地镜像
ubuntu@VM-0-4-ubuntu:~$ sudo docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
ubuntu              latest              74435f89ab78        8 days ago          73.9MB
ubuntu              18.04               8e4ce0a6ce69        8 days ago          64.2MB
tomcat              9.0                 2eb5a120304e        2 weeks ago         647MB
tomcat              latest              2eb5a120304e        2 weeks ago         647MB
nginx               latest              2622e6cca7eb        2 weeks ago         132MB
mysql               8.0.20              be0dbf01a0f3        2 weeks ago         541MB
mysql               latest              be0dbf01a0f3        2 weeks ago         541MB
hello-world         latest              bf756fb1ae65        5 months ago        13.3kB

# 运行版本为 8.0.20 的MySQL : docker run mysql:8.0.20
# --name mysql8 : 将容器命名为mysql8，后面可以用这个name进行容器的启动暂停等操作
# -e MYSQL_ROOT_PASSWORD=123456 : 设置MySQL密码为123456
# -d : 此容器在后台运行,并且返回容器的ID
# -i : 以交互模式运行容器
# -p : 进行端口映射，格式为主机(宿主)端口:容器端口
# --restart=always : 当docker重启时，该容器自动重启
ubuntu@VM-0-4-ubuntu:~$ sudo docker run --name mysql8 -e MYSQL_ROOT_PASSWORD=123456 -d -i -p 3300:3306 mysql:8.0.20 /bin/bash
1bbb0efc4f541b3fd46abcdd8a260ca672a2b0c49265962a37718814f57af16e

# 再次查看正在运行的容器，验证 MySQL 容器是否启动
ubuntu@VM-0-4-ubuntu:~$ sudo docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                               NAMES
1bbb0efc4f54        mysql:8.0.20        "docker-entrypoint.s…"   5 seconds ago       Up 3 seconds        33060/tcp, 0.0.0.0:3300->3306/tcp   mysql8
```
> 启动 MySQL 容器后，稍等片刻再次通过 `docker ps` 查看时会发现 MySQL 容器已经停止运行，其问题分析及解决方法见该文章最后的`参考`文章.


### 4.进入 MySQL 容器
```shell
# 进入后台正在运行的 MySQL 容器
# -i, --interactive=false : 以交互模式运行容器，通常与 -t 同时使用
# -t, --tty=false : 为容器重新分配一个伪输入终端，通常与 -i 同时使用
ubuntu@VM-0-4-ubuntu:~$ sudo docker exec -it mysql8 /bin/bash
root@1bbb0efc4f54:/# ls
bin   dev                         entrypoint.sh  home  lib64  mnt  proc  run   srv  tmp  var
boot  docker-entrypoint-initdb.d  etc            lib   media  opt  root  sbin  sys  usr

# 登录 MySQL : 失败了!
root@8fd4dc9f3b89:/# mysql -u root -p
Enter password:
ERROR 2002 (HY000): Can't connect to local MySQL server through socket '/var/run/mysqld/mysqld.sock' (2)
```

> 登录 MySQL 时失败了，抛出的异常信息为 : `ERROR 2002 (HY000): Can't connect to local MySQL server through socket '/var/run/mysqld/mysqld.sock' (2)`. Google 了很多资料也没解决该问题...


### 参考
> [Docker容器一起动就退出的解决方案](https://blog.csdn.net/weixin_34356310/article/details/92178989)

> [针对Docker运行启动就退出问题一站式解决方案](https://blog.csdn.net/u010358168/article/details/81347927)

---
title: Docker安装Redis及基本使用
date: 2020-06-24 19:04:37
tags: [Docker,Redis]
---


### 1. 搜索 Redis 镜像
```shell
ubuntu@VM-0-4-ubuntu:~$ sudo docker search redis
NAME                             DESCRIPTION                                     STARS               OFFICIAL               AUTOMATED
redis                            Redis is an open source key-value store that…   8316                [OK]
bitnami/redis                    Bitnami Redis Docker Image                      149                 [OK]
sameersbn/redis                                                                  80                  [OK]

. . . . .
```

### 2. 拉取 Redis 镜像
```shell
# 拉取最新版本的 redis 镜像
ubuntu@VM-0-4-ubuntu:~$ sudo docker pull redis
Using default tag: latest
latest: Pulling from library/redis
8559a31e96f4: Already exists
85a6a5c53ff0: Pull complete
b69876b7abed: Pull complete
a72d84b9df6a: Pull complete
5ce7b314b19c: Pull complete
04c4bfb0b023: Pull complete
Digest: sha256:800f2587bf3376cb01e6307afe599ddce9439deafbd4fb8562829da96085c9c5
Status: Downloaded newer image for redis:latest
docker.io/library/redis:latest

# 查看本地镜像，验证是否成功拉取 redis 镜像
ubuntu@VM-0-4-ubuntu:~$ sudo docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
ubuntu              latest              74435f89ab78        8 days ago          73.9MB
ubuntu              18.04               8e4ce0a6ce69        8 days ago          64.2MB
redis               latest              235592615444        2 weeks ago         104MB
tomcat              9.0                 2eb5a120304e        2 weeks ago         647MB
tomcat              latest              2eb5a120304e        2 weeks ago         647MB
nginx               latest              2622e6cca7eb        2 weeks ago         132MB
mysql               8.0.20              be0dbf01a0f3        2 weeks ago         541MB
mysql               latest              be0dbf01a0f3        2 weeks ago         541MB
hello-world         latest              bf756fb1ae65        5 months ago        13.3kB
```

### 3. 运行 Redis 容器
```shell
# 后台启动 redis 容器
# --name redis-test : 将容器命名为redis-test，后面可以用这个name进行容器的启动暂停等操作
# -i, --interactive=false : 以交互模式运行容器，通常与 -t 同时使用
# -t, --tty=false : 为容器重新分配一个伪输入终端，通常与 -i 同时使用
# -d : 此容器在后台运行,并且返回容器的ID
# -p : 进行端口映射，格式为主机(宿主)端口:容器端口
ubuntu@VM-0-4-ubuntu:~$ sudo docker run -itd --name redis-test -p 6380:6379 redis
231025907f4de3a4ecc1fb7a4bf4f09a204d0a777a657b6fddcf12ba938e74cb

# 验证 redis 容器是否已经启动
ubuntu@VM-0-4-ubuntu:~$ sudo docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                    NAMES
231025907f4d        redis               "docker-entrypoint.s…"   48 seconds ago      Up 46 seconds       0.0.0.0:6380->6379/tcp   redis-test
```

### 4. 进入 Redis 容器
```shell
# 查看当前正在运行的容器
ubuntu@VM-0-4-ubuntu:~$ sudo docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                    NAMES
231025907f4d        redis               "docker-entrypoint.s…"   5 minutes ago       Up 5 minutes        0.0.0.0:6380->6379/tcp   redis-test

# 进入后台正在运行的 redis 容器
# -i, --interactive=false : 以交互模式运行容器，通常与 -t 同时使用
# -t, --tty=false : 为容器重新分配一个伪输入终端，通常与 -i 同时使用
ubuntu@VM-0-4-ubuntu:~$ sudo docker exec -it redis-test /bin/bash
root@231025907f4d:/data# ls
root@231025907f4d:/data#

# redis 容器内 : 连接并测试 reids 客户端
root@231025907f4d:/data# redis-cli
127.0.0.1:6379> set firstKey firstValue
OK
127.0.0.1:6379> get firstKey
"firstValue"

# 主机 : 连接并测试 redis 客户端
root@231025907f4d:/data# exit # 退出 redis 容器，返回到宿主主机
exit
ubuntu@VM-0-4-ubuntu:~$ redis-cli -p 6380 # 连接 redis 客户端并指定端口为 6380( 其将会映射到 redis 容器中的 6379 端口 )
127.0.0.1:6380> get firstKey
"firstValue"
127.0.0.1:6380>

# 测试完毕后记得关闭 redis 容器哟
ubuntu@VM-0-4-ubuntu:~$ sudo docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                    NAMES
231025907f4d        redis               "docker-entrypoint.s…"   17 minutes ago      Up 17 minutes       0.0.0.0:6380->6379/tcp   redis-test
ubuntu@VM-0-4-ubuntu:~$ sudo docker stop 231025907f4d
231025907f4d
ubuntu@VM-0-4-ubuntu:~$ sudo docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS
 NAMES
```

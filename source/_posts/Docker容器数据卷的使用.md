---
title: Docker容器数据卷的使用
date: 2020-06-25 22:28:38
tags: [Docker]
---

## 数据卷的使用方式
*使用容器数据卷的好处 : 当部署 `nginx`，`tomcat` 或 `redis` 等容器时，可使用数据卷将容器中的配置文件自动同步到本地主机上，进而可解决当需要修改容器中的配置文件时需频繁进入容器内部的问题，即提高了工作效率 !*

### 方式一
1. 直接使用命令来挂载 : `docker run -it -v 主机目录:容器目录`
```shell
# 主机terminal(1)
# 查看本地镜像
ubuntu@VM-0-4-ubuntu:/home$ sudo docker images
REPOSITORY            TAG                 IMAGE ID            CREATED             SIZE
mytomcat              0.1                 1a8ec5515a1d        23 minutes ago      652MB
ubuntu                latest              74435f89ab78        8 days ago          73.9MB
ubuntu                18.04               8e4ce0a6ce69        8 days ago          64.2MB
redis                 latest              235592615444        2 weeks ago         104MB
tomcat                9                   2eb5a120304e        2 weeks ago         647MB
tomcat                9.0                 2eb5a120304e        2 weeks ago         647MB
tomcat                latest              2eb5a120304e        2 weeks ago         647MB
nginx                 latest              2622e6cca7eb        2 weeks ago         132MB
mysql                 8.0.20              be0dbf01a0f3        2 weeks ago         541MB
mysql                 latest              be0dbf01a0f3        2 weeks ago         541MB
portainer/portainer   latest              cd645f5a4769        3 weeks ago         79.1MB
hello-world           latest              bf756fb1ae65        5 months ago        13.3kB

# 主机terminal(1)
# 查看 home 目录下有哪些文件
ubuntu@VM-0-4-ubuntu:/home$ ls
ishacker.net.java  ubuntu

# 主机terminal(1)
# 以后台运行的方式进入 ubuntu 容器，并将其 home 目录挂载到主机上的 home/testing 目录中 
ubuntu@VM-0-4-ubuntu:/home$ sudo docker run -it -v /home/testing:/home ubuntu /bin/bash
root@9ecaeb8de732:/# cd home/ # 进入 home 目录，发现该目录下无任何文件
root@9ecaeb8de732:/home# ls
root@9ecaeb8de732:/home#

---

# 打开一个新的主机terminal(2)
# 再次查看主机 home 目录下有哪些文件
ubuntu@VM-0-4-ubuntu:~$ cd /home/
ubuntu@VM-0-4-ubuntu:/home$ ls
ishacker.net.java  testing  ubuntu # 会发现 home 目录下多了一个 testing 文件夹，且 testing 文件夹为空( 因为 ubuntu 容器中的 home 目录下为空 )
ubuntu@VM-0-4-ubuntu:/home$ cd testing/
ubuntu@VM-0-4-ubuntu:/home/testing$ ls
ubuntu@VM-0-4-ubuntu:/home/testing$
```

2. 通过命令 `docker inspect <容器ID>` 获取容器/镜像的元数据，进而查看数据卷是否挂载成功.
```shell
# 查看当前正在运行的容器
ubuntu@VM-0-4-ubuntu:/home/testing$ sudo docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
9ecaeb8de732        ubuntu              "/bin/bash"         12 minutes ago      Up 12 minutes                           gallant_cartwright

# 获取 ubuntu 容器的元数据
ubuntu@VM-0-4-ubuntu:/home/testing$ sudo docker inspect 9ecaeb8de732
[
    {
        "Mounts": [ # 数据卷挂载的配置信息
            {
                "Type": "bind",
                "Source": "/home/testing", # 源目录，即主机目录
                "Destination": "/home", # 目标目录，即容器目录
                "Mode": "",
                "RW": true,
                "Propagation": "rprivate"
            }
        ],
    }
``` 

3. 测试
```shell
# 容器terminal
# 查看 ubuntu 容器 home 目录下有哪些文件
ubuntu@VM-0-4-ubuntu:~$ sudo docker ps # 首先查看当前正在运行的容器
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
9ecaeb8de732        ubuntu              "/bin/bash"         29 minutes ago      Up 29 minutes                           gallant_cartwright
ubuntu@VM-0-4-ubuntu:~$ sudo docker exec -it 9ecaeb8de732 /bin/bash # 进入后台正在运行的 ubuntu 容器中
root@9ecaeb8de732:/# ls # 查看 ubuntu 根目录有哪些文件
bin  boot  dev  etc  home  lib  lib32  lib64  libx32  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
root@9ecaeb8de732:/# cd home/ # 进入 home 目录
root@9ecaeb8de732:/home# ls # 发现 home 目录下无任何文件
root@9ecaeb8de732:/home#

---

# 主机terminal
# 在主机 home/testing 目录下创建一个名为 bind.txt 文件，并写入一段话
ubuntu@VM-0-4-ubuntu:/home/testing$ su echo "hey guy good night" > bind.txt
ubuntu@VM-0-4-ubuntu:/home/testing$ su
Password:
root@VM-0-4-ubuntu:/home/testing# echo "hey guy good night" > bind.txt
root@VM-0-4-ubuntu:/home/testing# ls
bind.txt
root@VM-0-4-ubuntu:/home/testing# cat bind.txt
hey guy good night

---

# 容器terminal
# 再次查看 ubuntu 容器 home 目录下有哪些文件，会发现主机 home/testing 目录下的文件已经同步到了 ubuntu 容器中的 home 目录下
root@9ecaeb8de732:/home# ls
bind.txt
root@9ecaeb8de732:/home# cat bind.txt
hey guy good night
```

> 相反如果在 ubuntu 容器中的 home 目录下创建文件或更新数据后，主机上 home/testing 目录中的数据也能得到同步. 进而实现了如同 `Vue.js` 般的数据双向绑定 !  注意 : 就算在 ubuntu 容器停止运行的情况下修改主机上 /home/testing 目录下的数据，当再次启动 ubuntu 容器后其 home 目录下的数据依旧可以得到同步.

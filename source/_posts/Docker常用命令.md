---
title: Docker常用命令
date: 2020-06-24 09:37:52
tags: [Docker]
---


*图解 Docker 常用命令*

![ ](Docker常用命令/docker-command.png)


### 后台启动容器
```shell
# 查看所有镜像
ubuntu@VM-0-4-ubuntu:~$ sudo docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
ubuntu              latest              74435f89ab78        7 days ago          73.9MB
ubuntu              18.04               8e4ce0a6ce69        7 days ago          64.2MB
mysql               latest              be0dbf01a0f3        2 weeks ago         541MB
hello-world         latest              bf756fb1ae65        5 months ago        13.3kB

# 查看当前正在运行的容器
ubuntu@VM-0-4-ubuntu:~$ sudo docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES

# 后来运行ubuntu容器
ubuntu@VM-0-4-ubuntu:~$ sudo docker run -d ubuntu
6173c91e2589e3f43172805521a858e3ca242db72fcbf653849fac9216faf934

# 再次查看当前正在运行的容器，发现ubuntu并没有正在运行?!
# 这是因为docker没有发现应用便会自动停止，所以若要使得docker容器可以在后台启动并运行，则前台必须要有一个进程
ubuntu@VM-0-4-ubuntu:~$ sudo docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
```


### 查看日志
```shell
# 查看logs命令的帮助文档
ubuntu@VM-0-4-ubuntu:~$ sudo docker logs --help

Usage:  docker logs [OPTIONS] CONTAINER

Fetch the logs of a container

Options:
      --details        Show extra details provided to logs
  -f, --follow         Follow log output
      --since string   Show logs since timestamp (e.g. 2013-01-02T13:23:37) or relative (e.g. 42m for 42 minutes)
      --tail string    Number of lines to show from the end of the logs (default "all")
  -t, --timestamps     Show timestamps
      --until string   Show logs before a timestamp (e.g. 2013-01-02T13:23:37) or relative (e.g. 42m for 42 minutes)

# 查看当前正在运行的容器(无)
ubuntu@VM-0-4-ubuntu:~$ sudo docker ps                                                                                 
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES        

# 执行 docker run -d <容器名称> 后台启动 ubuntu 容器并在该容器中创建一个进程( 一段shell脚本 )以防止docker容器没有发现进程而自动停止
ubuntu@VM-0-4-ubuntu:~$ sudo docker run -d ubuntu /bin/sh -c "while true;do echo 'this is logs..';sleep 1;done"        
b89578415781997955293bbff1ccfc849bf8cad0f6af30193a16d51e576862b2                                                       
ubuntu@VM-0-4-ubuntu:~$ sudo docker ps                                                                                 
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS          NAMES      
b89578415781        ubuntu              "/bin/sh -c 'while t…"   9 seconds ago       Up 7 seconds                       jovial_nightingale            

# 根据指定容器id来查看容器的日志信息
# -tf : 显示日志
# --tail number : 显示日志的条数 
ubuntu@VM-0-4-ubuntu:~$ sudo docker logs -tf --tail 5 b89578415781                                                     
2020-06-24T02:01:55.077195969Z this is logs..                                                                          
2020-06-24T02:01:56.081186346Z this is logs..                                                                          
2020-06-24T02:01:57.085186008Z this is logs..                                                                          
2020-06-24T02:01:58.089245329Z this is logs..                                                                          
2020-06-24T02:01:59.093251030Z this is logs..                                                                          
2020-06-24T02:02:00.097191092Z this is logs..                                                                          
2020-06-24T02:02:01.101183828Z this is logs..                                                                          
2020-06-24T02:02:02.105495333Z this is logs..                                                                          
2020-06-24T02:02:03.109132666Z this is logs..                                                                          
^C  # ctrl + c                                                                                                                   
ubuntu@VM-0-4-ubuntu:~$
```


### 查看容器中的进程信息
```shell
# 查看当前正在运行的容器
ubuntu@VM-0-4-ubuntu:~$ sudo docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS              NAMES
5f39caf62a73        ubuntu              "/bin/sh -c 'while t…"   6 seconds ago       Up 4 seconds                           magical_leakey

# 根据容器id查看当前正在运行的ubuntu容器的进程信息
ubuntu@VM-0-4-ubuntu:~$ sudo docker top 5f39caf62a73
UID                 PID                 PPID                C                   STIME               TTY                 TIME                CMD
root                17430               17403               0                   10:14               ?                   00:00:00            /bin/sh -c while true;do echo 'this is logs..';sleep 1;done
root                17532               17430               0                   10:14               ?
 00:00:00            sleep 1
```


### 查看镜像的元数据
```shell
# 查看inspect的帮助命令
ubuntu@VM-0-4-ubuntu:~$ sudo docker inspect --help

Usage:  docker inspect [OPTIONS] NAME|ID [NAME|ID...]

Return low-level information on Docker objects

Options:
  -f, --format string   Format the output using the given Go template
  -s, --size            Display total file sizes if the type is container
      --type string     Return JSON for specified type

# 查看当前正在运行的容器
ubuntu@VM-0-4-ubuntu:~$ sudo docker ps                                                                                 
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS              NAMES
5f39caf62a73        ubuntu              "/bin/sh -c 'while t…"   7 minutes ago       Up 7 minutes                           magical_leakey

# 根据容器id查看当前正在运行的ubuntu镜像的元数据
ubuntu@VM-0-4-ubuntu:~$ sudo docker inspect 5f39caf62a73                                                               
[                                                                                                                      
    {                                                                                                                  
        "Id": "5f39caf62a73c3f59c2e153a2215d4b99520b9015955257e3a94ee758cb5ed4e",                                      
        "Created": "2020-06-24T02:14:16.639468062Z",                                                                   
        "Path": "/bin/sh",                                                                                             
        "Args": [                                                                                                      
            "-c",                                                                                                      
            "while true;do echo 'this is logs..';sleep 1;done"                                                         
        ],                                                                                                             
        "State": {                                                                                                     
            "Status": "running",                                                                                       
            "Running": true,                                                                                           
            "Paused": false,                                                                                           
            "Restarting": false,                                                                                       
            "OOMKilled": false,                                                                                        
            "Dead": false,                                                                                             
            "Pid": 17430,                                                                                              
            "ExitCode": 0,                                                                                             
            "Error": "",                                                                                               
            "StartedAt": "2020-06-24T02:14:18.033196262Z",                                                             
            "FinishedAt": "0001-01-01T00:00:00Z"                                                                       
        }

. . . . . .                                                                                  
```


### 进入当前正在运行的容器
```shell
# 查看当前正在运行的容器
ubuntu@VM-0-4-ubuntu:~$ sudo docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS              NAMES
5f39caf62a73        ubuntu              "/bin/sh -c 'while t…"   33 minutes ago      Up 33 minutes                          magical_leakey

# 方式一 : docker exec -it <容器id> /bin/bash
# 根据id进入当前正在运行的ubuntu容器，特征为进入容器后开启一个新的终端
ubuntu@VM-0-4-ubuntu:~$ sudo docker exec -it 5f39caf62a73 /bin/bash
root@5f39caf62a73:/# ls
bin  boot  dev  etc  home  lib  lib32  lib64  libx32  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var


# 方式二 : docker attach <容器id>
# 根据id进入当前正在运行的ubuntu容器，特征为进入一个容器正在执行的终端
ubuntu@VM-0-4-ubuntu:~$ sudo docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS
      NAMES
ed8c6b026ee1        ubuntu              "/bin/sh -c 'while t…"   7 seconds ago       Up 5 seconds
      stupefied_boyd
ubuntu@VM-0-4-ubuntu:~$ sudo docker attach ed8c6b026ee1
this is logs..
this is logs..
this is logs..
this is logs..
this is logs..
this is logs..
^Cubuntu@VM-0-4-ubuntu:~$
```


### 从容器内拷贝文件到主机上
```shell
# 查看当前正在运行的容器(无)
ubuntu@VM-0-4-ubuntu:/home$ sudo docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS              NAMES

# 查看所有镜像
ubuntu@VM-0-4-ubuntu:/home$ sudo docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
ubuntu              latest              74435f89ab78        7 days ago          73.9MB
ubuntu              18.04               8e4ce0a6ce69        7 days ago          64.2MB
mysql               latest              be0dbf01a0f3        2 weeks ago         541MB
hello-world         latest              bf756fb1ae65        5 months ago        13.3kB

# 进入名为ubuntu的docker容器
ubuntu@VM-0-4-ubuntu:/home$ sudo docker run -it ubuntu /bin/bash
root@2f8d86d683f2:/# cd home/
root@2f8d86d683f2:/home# ls
root@2f8d86d683f2:/home# touch ishacker.net.java # 在容器/home目录下创建一个名为 ishacker.net.java 的文件
root@2f8d86d683f2:/home# ls
ishacker.net.java
root@2f8d86d683f2:/home# exit # 退出容器，返回主机terminal
exit

# 查看容器的运行历史记录
ubuntu@VM-0-4-ubuntu:/home$ sudo docker ps -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                PORTS               NAMES
2f8d86d683f2        ubuntu              "/bin/bash"              51 seconds ago      Exited (0) 13 seconds ago                  vibrant_lumiere
ed8c6b026ee1        ubuntu              "/bin/sh -c 'while t…"   11 minutes ago      Exited (130) 11 minutes ago                stupefied_boyd

# 将刚刚进入的，id为 2f8d86d683f2 容器中的 ishacker.net.java 文件拷贝到当前主机的/home目录中
ubuntu@VM-0-4-ubuntu:/home$ ls
ubuntu
ubuntu@VM-0-4-ubuntu:/home$ sudo docker cp 2f8d86d683f2:/home/ishacker.net.java /home
ubuntu@VM-0-4-ubuntu:/home$ ls
ishacker.net.java  ubuntu
```


### 小结
其中 <> 括起来的参数为必选，而 [] 括起来为可选.

* `docker version` : 查看docker的版本号，包括客户端、服务端、依赖的Go等
* `docker info` : 查看系统(docker)层面信息，包括管理的images, containers数等
* `docker search <image>` : 在docker index中搜索image
* `docker pull <image>` : 从docker registry server 中下拉image
* `docker inspect <image|container>` : 查看image或container的底层信息
* `docker images -a` : 列出所有的images
* `docker ps` : 默认显示正在运行中的container
* `docker ps -l` : 显示最后一次创建的container，包括未运行的
* `docker ps -a` : 显示所有的container，包括未运行的
* `docker logs <container>` : 查看container的日志，也就是执行命令的一些输出
* `docker rm <container...>` : 删除一个或多个container
* `docker rm docker ps -a -q` 删除所有的container
* `docker ps -a -q | xargs docker rm` : 同上, 删除所有的container
* `docker rmi <image...>` : 删除一个或多个image
* `docker start/stop/restart <container>` : 开启/停止/重启container
* `docker start -i <container>` : 启动一个container并进入交互模式
* `docker attach <container>` : attach一个运行中的container
* `docker run <image> <command>` : 使用image创建container并执行相应命令，然后停止
* `docker run -i -t <image> /bin/bash` : 使用image创建container并进入交互模式, login shell是/bin/bash
* `docker run -i -t -p <host_port:contain_port>` : 将container的端口映射到宿主机的端口


### 参考
更多 Docker 常用命令总结请参考如下两篇文章 : 
* ha97.com/5546.html
* https://www.cnblogs.com/Wshile/p/12988720.html

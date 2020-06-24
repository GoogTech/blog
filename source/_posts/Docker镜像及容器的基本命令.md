---
title: Docker镜像及容器的基本命令
date: 2020-06-23 16:49:34
tags: [Docker]
---

## Docker帮助命令
```shell
# 查看docker的版本信息
$ docker version
# 查看docker的系统信息
$ docker info
# 查看docker帮助命令
$ docker --help
```


## Docker镜像命令
### docker images : 查看镜像
```shell
# docs
ubuntu@VM-0-4-ubuntu:~$ sudo docker images --help

Usage:  docker images [OPTIONS] [REPOSITORY[:TAG]]

List images

Options:
  -a, --all             Show all images (default hides intermediate images)
      --digests         Show digests
  -f, --filter filter   Filter output based on conditions provided
      --format string   Pretty-print images using a Go template
      --no-trunc        Don't truncate output
  -q, --quiet           Only show numeric IDs

# image
ubuntu@VM-0-4-ubuntu:~$ sudo docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
hello-world         latest              bf756fb1ae65        5 months ago        13.3kB

# image -a
ubuntu@VM-0-4-ubuntu:~$ sudo docker images -a
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
hello-world         latest              bf756fb1ae65        5 months ago        13.3kB

# image -q
ubuntu@VM-0-4-ubuntu:~$ sudo docker images -q
bf756fb1ae65
```


### docker search : 搜索镜像
```shell
# docs
ubuntu@VM-0-4-ubuntu:~$ sudo docker search --help

Usage:  docker search [OPTIONS] TERM

Search the Docker Hub for images

Options:
  -f, --filter filter   Filter output based on conditions provided
      --format string   Pretty-print search using a Go template
      --limit int       Max number of search results (default 25)
      --no-trunc        Don't truncate output

# 如同在dockerhub网页上搜索: https://hub.docker.com/search?q=mysql&type=image
ubuntu@VM-0-4-ubuntu:~$ sudo docker search mysql
NAME                              DESCRIPTION                                     STARS               OFFICIAL          AUTOMATED
mysql                             MySQL is a widely used, open-source relation…   9662                [OK]
mariadb                           MariaDB is a community-developed fork of MyS…   3514                [OK]
. . . . . .


# STARS > 9000
ubuntu@VM-0-4-ubuntu:~$ sudo docker search mysql --filter=STARS=9000
NAME                DESCRIPTION                                     STARS               OFFICIAL            AUTOMATED
mysql               MySQL is a widely used, open-source relation…   9662                [OK]
```


### docker pull : 下载镜像
```shell
# docs
ubuntu@VM-0-4-ubuntu:~$ sudo docker pull --help

Usage:  docker pull [OPTIONS] NAME[:TAG|@DIGEST]

Pull an image or a repository from a registry

Options:
  -a, --all-tags                Download all tagged images in the repository
      --disable-content-trust   Skip image verification (default true)
      --platform string         Set platform if server is multi-platform capable
  -q, --quiet                   Suppress verbose output

# download mysql and the default tag is latest
# or download mysql and the tag is 5.7: docker pull mysql[:tag]
ubuntu@VM-0-4-ubuntu:~$ sudo docker pull mysql 
Using default tag: latest
latest: Pulling from library/mysql
8559a31e96f4: Pull complete
d51ce1c2e575: Pull complete
c2344adc4858: Pull complete
fcf3ceff18fc: Pull complete
16da0c38dc5b: Pull complete
b905d1797e97: Pull complete
4b50d1c6b05c: Pull complete
c75914a65ca2: Pull complete
1ae8042bdd09: Pull complete
453ac13c00a3: Pull complete
9e680cd72f08: Pull complete
a6b5dc864b6c: Pull complete
Digest: sha256:8b7b328a7ff6de46ef96bcf83af048cb00a1c86282bfca0cb119c84568b4caf6
Status: Downloaded newer image for mysql:latest
docker.io/library/mysql:latest # docker pull mysql == docker pull docker.io/library/mysql:latest

# list all images
ubuntu@VM-0-4-ubuntu:~$ sudo docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
mysql               latest              be0dbf01a0f3        2 weeks ago         541MB
hello-world         latest              bf756fb1ae65        5 months ago        13.3kB
```


### docker rmi : 删除镜像
```shell
# docs
ubuntu@VM-0-4-ubuntu:~$ sudo docker rmi --help

Usage:  docker rmi [OPTIONS] IMAGE [IMAGE...]

Remove one or more images

Options:
  -f, --force      Force removal of the image
      --no-prune   Do not delete untagged parents

# list all images      
ubuntu@VM-0-4-ubuntu:~$ sudo docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
mysql               latest              be0dbf01a0f3        2 weeks ago         541MB
hello-world         latest              bf756fb1ae65        5 months ago        13.3kB

# delete the image by id: rmi -f [IMAGE ID]
ubuntu@VM-0-4-ubuntu:~$ sudo docker rmi -f bf756fb1ae65 # hello-world
Untagged: hello-world:latest
Untagged: hello-world@sha256:d58e752213a51785838f9eed2b7a498ffa1cb3aa7f946dda11af39286c3db9a9
Deleted: sha256:bf756fb1ae65adf866bd8c456593cd24beb6a0a061dedf42b26a993176745f6b

# list all images
ubuntu@VM-0-4-ubuntu:~$ sudo docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
mysql               latest              be0dbf01a0f3        2 weeks ago         541MB
```

* 删除指定id的镜像 : `docker rmi -f 容器id`
* 批量删除指定id的镜像 : `docker rmi -f 容器id, 容器id, 容器id`
* 删除全部镜像，其中 `docker images -aq` 的功能为列出全部镜像的id : `docker rmi -f $(docker images -aq)`


## Docker容器命令
### 新建容器并启动
1. 有了镜像之后才可以创建容器，所以首先我们需要先下载一个镜像，这里我选择版本为 18.04 的 Ubuntu 镜像 :
```shell
# download
ubuntu@VM-0-4-ubuntu:~$ sudo docker pull ubuntu:18.04
18.04: Pulling from library/ubuntu
d7c3167c320d: Pull complete
131f805ec7fd: Pull complete
322ed380e680: Pull complete
6ac240b13098: Pull complete
Digest: sha256:86510528ab9cd7b64209cbbe6946e094a6d10c6db21def64a93ebdd20011de1d
Status: Downloaded newer image for ubuntu:18.04
docker.io/library/ubuntu:18.04

# check
ubuntu@VM-0-4-ubuntu:~$ sudo docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
ubuntu              18.04               8e4ce0a6ce69        6 days ago          64.2MB
mysql               latest              be0dbf01a0f3        2 weeks ago         541MB
```

2. 查看容器命令帮助文档
```shell
# docs
ubuntu@VM-0-4-ubuntu:~$ docker run --help

Usage:  docker run [OPTIONS] IMAGE [COMMAND] [ARG...]

Run a command in a new container

Options:
      --add-host list                  Add a custom host-to-IP mapping (host:ip)
  -a, --attach list                    Attach to STDIN, STDOUT or STDERR
      --blkio-weight uint16            Block IO (relative weight), between 10 and 1000, or 0 to disable (default 0)
      --blkio-weight-device list       Block IO weight (relative device weight) (default [])
      --cap-add list                   Add Linux capabilities
      --cap-drop list                  Drop Linux capabilities
      --cgroup-parent string           Optional parent cgroup for the container
      --cidfile string                 Write the container ID to the file
      --cpu-period int                 Limit CPU CFS (Completely Fair Scheduler) period
      --cpu-quota int                  Limit CPU CFS (Completely Fair Scheduler) quota
      --cpu-rt-period int              Limit CPU real-time period in microseconds
      --cpu-rt-runtime int             Limit CPU real-time runtime in microseconds
  -c, --cpu-shares int                 CPU shares (relative weight)
      --cpus decimal                   Number of CPUs
      --cpuset-cpus string             CPUs in which to allow execution (0-3, 0,1)
      --cpuset-mems string             MEMs in which to allow execution (0-3, 0,1)
  -d, --detach                         Run container in background and print container ID
      --detach-keys string             Override the key sequence for detaching a container
      --device list                    Add a host device to the container
      --device-cgroup-rule list        Add a rule to the cgroup allowed devices list
      --device-read-bps list           Limit read rate (bytes per second) from a device (default [])
      --device-read-iops list          Limit read rate (IO per second) from a device (default [])
      --device-write-bps list          Limit write rate (bytes per second) to a device (default [])
      --device-write-iops list         Limit write rate (IO per second) to a device (default [])
      --disable-content-trust          Skip image verification (default true)
      --dns list                       Set custom DNS servers
      --dns-option list                Set DNS options
      --dns-search list                Set custom DNS search domains
      --domainname string              Container NIS domain name
      --entrypoint string              Overwrite the default ENTRYPOINT of the image
  -e, --env list                       Set environment variables
      --env-file list                  Read in a file of environment variables
      --expose list                    Expose a port or a range of ports
      --gpus gpu-request               GPU devices to add to the container ('all' to pass all GPUs)
      --group-add list                 Add additional groups to join
      --health-cmd string              Command to run to check health
      --health-interval duration       Time between running the check (ms|s|m|h) (default 0s)
      --health-retries int             Consecutive failures needed to report unhealthy
      --health-start-period duration   Start period for the container to initialize before starting
                                       health-retries countdown (ms|s|m|h) (default 0s)
      --health-timeout duration        Maximum time to allow one check to run (ms|s|m|h) (default 0s)
      --help                           Print usage
  -h, --hostname string                Container host name
      --init                           Run an init inside the container that forwards signals and reaps processes
  -i, --interactive                    Keep STDIN open even if not attached
      --ip string                      IPv4 address (e.g., 172.30.100.104)
      --ip6 string                     IPv6 address (e.g., 2001:db8::33)
      --ipc string                     IPC mode to use
      --isolation string               Container isolation technology
      --kernel-memory bytes            Kernel memory limit
  -l, --label list                     Set meta data on a container
      --label-file list                Read in a line delimited file of labels
      --link list                      Add link to another container
      --link-local-ip list             Container IPv4/IPv6 link-local addresses
      --log-driver string              Logging driver for the container
      --log-opt list                   Log driver options
      --mac-address string             Container MAC address (e.g., 92:d0:c6:0a:29:33)
  -m, --memory bytes                   Memory limit
      --memory-reservation bytes       Memory soft limit
      --memory-swap bytes              Swap limit equal to memory plus swap: '-1' to enable unlimited swap
      --memory-swappiness int          Tune container memory swappiness (0 to 100) (default -1)
      --mount mount                    Attach a filesystem mount to the container
      --name string                    Assign a name to the container
      --network network                Connect a container to a network
      --network-alias list             Add network-scoped alias for the container
      --no-healthcheck                 Disable any container-specified HEALTHCHECK
      --oom-kill-disable               Disable OOM Killer
      --oom-score-adj int              Tune host's OOM preferences (-1000 to 1000)
      --pid string                     PID namespace to use
      --pids-limit int                 Tune container pids limit (set -1 for unlimited)
      --platform string                Set platform if server is multi-platform capable
      --privileged                     Give extended privileges to this container
  -p, --publish list                   Publish a container's port(s) to the host
  -P, --publish-all                    Publish all exposed ports to random ports
      --read-only                      Mount the container's root filesystem as read only
      --restart string                 Restart policy to apply when a container exits (default "no")
      --rm                             Automatically remove the container when it exits
      --runtime string                 Runtime to use for this container
      --security-opt list              Security Options
      --shm-size bytes                 Size of /dev/shm
      --sig-proxy                      Proxy received signals to the process (default true)
      --stop-signal string             Signal to stop a container (default "SIGTERM")
      --stop-timeout int               Timeout (in seconds) to stop a container
      --storage-opt list               Storage driver options for the container
      --sysctl map                     Sysctl options (default map[])
      --tmpfs list                     Mount a tmpfs directory
  -t, --tty                            Allocate a pseudo-TTY
      --ulimit ulimit                  Ulimit options (default [])
  -u, --user string                    Username or UID (format: <name|uid>[:<group|gid>])
      --userns string                  User namespace to use
      --uts string                     UTS namespace to use
  -v, --volume list                    Bind mount a volume
      --volume-driver string           Optional volume driver for the container
      --volumes-from list              Mount volumes from the specified container(s)
  -w, --workdir string                 Working directory inside the container
```

`docker run [可选参数] image`，其参数说明如下所示 :
* --name="customName"   容器命名
* --d                   后台方式运行
* --it                  交互方式运行
  * docker run -it ubuntu /bin/bash # 启动并进入容器
* -p                    指定容器的端口
  * -p ip:主机端口:容器端口
  * -p 主机端口:容器端口
  * -p 容器端口
  * 容器端口
* -P                    随机指定端口

3. 测试
```shell
# 启动并进入容器
ubuntu@VM-0-4-ubuntu:~$ sudo docker run -it ubuntu /bin/bash

# 查看容器中的内容
root@a90466e5e8cb:/# ls -la
total 56
drwxr-xr-x   1 root root 4096 Jun 23 22:09 .
drwxr-xr-x   1 root root 4096 Jun 23 22:09 ..
-rwxr-xr-x   1 root root    0 Jun 23 22:09 .dockerenv
lrwxrwxrwx   1 root root    7 Jun  6 01:18 bin -> usr/bin
drwxr-xr-x   2 root root 4096 Apr 15 11:09 boot
drwxr-xr-x   5 root root  360 Jun 23 22:09 dev
drwxr-xr-x   1 root root 4096 Jun 23 22:09 etc
drwxr-xr-x   2 root root 4096 Apr 15 11:09 home
lrwxrwxrwx   1 root root    7 Jun  6 01:18 lib -> usr/lib
lrwxrwxrwx   1 root root    9 Jun  6 01:18 lib32 -> usr/lib32
lrwxrwxrwx   1 root root    9 Jun  6 01:18 lib64 -> usr/lib64
lrwxrwxrwx   1 root root   10 Jun  6 01:18 libx32 -> usr/libx32
drwxr-xr-x   2 root root 4096 Jun  6 01:18 media
drwxr-xr-x   2 root root 4096 Jun  6 01:18 mnt
drwxr-xr-x   2 root root 4096 Jun  6 01:18 opt
dr-xr-xr-x 116 root root    0 Jun 23 22:09 proc
drwx------   2 root root 4096 Jun  6 01:21 root
drwxr-xr-x   1 root root 4096 Jun 17 01:20 run
lrwxrwxrwx   1 root root    8 Jun  6 01:18 sbin -> usr/sbin
drwxr-xr-x   2 root root 4096 Jun  6 01:18 srv
dr-xr-xr-x  13 root root    0 Jun 23 22:09 sys
drwxrwxrwt   2 root root 4096 Jun  6 01:21 tmp
drwxr-xr-x   1 root root 4096 Jun  6 01:18 usr
drwxr-xr-x   1 root root 4096 Jun  6 01:21 var

# 退出容器
root@a90466e5e8cb:/# exit
exit
ubuntu@VM-0-4-ubuntu:~$
```


### 列出所有运行的容器
```shell
# 查看所有正在运行的容器(无)
ubuntu@VM-0-4-ubuntu:~$ sudo docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                      PORTS      NAMES

# 查看曾经运行过的容器
ubuntu@VM-0-4-ubuntu:~$ sudo docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                      PORTS      NAMES
a90466e5e8cb        ubuntu              "/bin/bash"         6 minutes ago       Exited (0) 4 minutes ago               objective_goldstine
9849ee4d02bf        ubuntu              "/bin/bash"         9 minutes ago       Exited (0) 6 minutes ago               vibrant_sinoussi
aaaee4577665        bf756fb1ae65        "/hello"            14 hours ago        Exited (0) 14 hours ago                modest_ardinghelli
d9dbd1de7f99        bf756fb1ae65        "/hello"            40 hours ago        Exited (0) 40 hours ago                laughing_cerf
8001fae4d389        bf756fb1ae65        "/hello"            2 days ago          Exited (0) 2 days ago                  naughty_hoover
6afccb969d87        bf756fb1ae65        "/hello"            2 days ago          Exited (0) 2 days ago                  silly_pare

# 查看一个最近创建的容器( n=? )
ubuntu@VM-0-4-ubuntu:~$ sudo docker ps -a -n=1
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                       PORTS      NAMES
a90466e5e8cb        ubuntu              "/bin/bash"         11 minutes ago      Exited (0) 9 minutes ago                objective_goldstine

# 查看所有容器的编号( 结合 -a 与 -q )
ubuntu@VM-0-4-ubuntu:~$ sudo docker ps -aq
a90466e5e8cb
9849ee4d02bf
aaaee4577665
d9dbd1de7f99
8001fae4d389
6afccb969d87
```


### 退出容器
```shell
# 1. exit: 直接停止并退出容器
# 查看当前正在运行的容器(无)
ubuntu@VM-0-4-ubuntu:~$ sudo docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS              NAMES

# 查看所有镜像
ubuntu@VM-0-4-ubuntu:~$ sudo docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
ubuntu              latest              74435f89ab78        6 days ago          73.9MB
ubuntu              18.04               8e4ce0a6ce69        6 days ago          64.2MB
mysql               latest              be0dbf01a0f3        2 weeks ago         541MB

# 启动并进入容器
ubuntu@VM-0-4-ubuntu:~$ sudo docker run -it ubuntu /bin/bash
root@3b93a1830f1e:/# ls
bin  boot  dev  etc  home  lib  lib32  lib64  libx32  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var

# exit
root@3b93a1830f1e:/# exit
exit
ubuntu@VM-0-4-ubuntu:~$

# 查看当前正在运行的容器(无)
ubuntu@VM-0-4-ubuntu:~$ sudo docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS              NAMES

---

# 2. Ctrl + P + Q: 退出后容器不停止
# 查看当前正在运行的容器(无)
ubuntu@VM-0-4-ubuntu:~$ sudo docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS              NAMES

# 启动并进入容器
ubuntu@VM-0-4-ubuntu:~$ sudo docker run -it ubuntu /bin/bash
root@3b93a1830f1e:/# ls
bin  boot  dev  etc  home  lib  lib32  lib64  libx32  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var

# Ctrl + P + Q
ubuntu@VM-0-4-ubuntu:~$

# 再次查看当前正在运行的容器
ubuntu@VM-0-4-ubuntu:~$ sudo docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS              NAMES
8715a6647cba        ubuntu              "/bin/bash"         58 seconds ago      Up 56 seconds                          eager_villani
```


### 删除容器
* `docker rm 容器id` : 根据id删除指定的容器，如果要删除运行中的容器需要使用命令 `docker rm -f 容器id`
* `docker rm -f $(docker ps -aq)` : 根据id删除所有的容器，其中` docker ps -aq` 的作用为查出所有容器的id


### 容器的启动和停止
```shell
# 1. docker start 容器id : 启动容器
# 查看当前正在运行的容器(无)
ubuntu@VM-0-4-ubuntu:~$ sudo docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS              NAMES

# 查看容器的运行历史记录
ubuntu@VM-0-4-ubuntu:~$ sudo docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                          PORTS   NAMES
cceb83ac32f9        hello-world         "/hello"            11 minutes ago      Exited (0) 11 minutes ago               laughing_gates
8715a6647cba        ubuntu              "/bin/bash"         25 minutes ago      Exited (0) About a minute ago           eager_villani
3b93a1830f1e        ubuntu              "/bin/bash"         2 hours ago         Exited (0) 2 hours ago                  wizardly_goldberg

# 运行id为8715a6647cba的容器            
ubuntu@VM-0-4-ubuntu:~$ sudo docker start 8715a6647cba
8715a6647cba

# 再次查看当前正在运行的容器，发现id为8715a6647cba的容器已经开始运行
ubuntu@VM-0-4-ubuntu:~$ sudo docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS
 NAMES
8715a6647cba        ubuntu              "/bin/bash"         26 minutes ago      Up 4 seconds
 eager_villani

---

# 2. docker restart 容器id : 重启容器

---

# 3. docker stop 容器id : 停止容器
# 查看当前正在运行的容器
ubuntu@VM-0-4-ubuntu:~$ sudo docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
8715a6647cba        ubuntu              "/bin/bash"         30 minutes ago      Up 4 minutes                            eager_villani

# 停止id为8715a6647cba的容器
ubuntu@VM-0-4-ubuntu:~$ sudo docker stop 8715a6647cba
8715a6647cba

# 再次查看当前正在运行的容器
ubuntu@VM-0-4-ubuntu:~$ sudo docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES

---

# 4. docker kill 容器id : 强制停止容器
```


## 参考
> [Docker Base Command](https://docs.docker.com/engine/reference/commandline/docker/)

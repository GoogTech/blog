---
title: Docker Dockerfile介绍及指令说明
date: 2020-06-26 11:37:53
tags: [Docker]
---


### Dockerfile介绍
![ ](Dockerfile介绍及指令说明/Docker-Dockerfile-build-1.jpg)

`Dockerfile`是一个用来构建镜像的文本文件，文本内容包含了一条条构建镜像所需的指令和说明. 例如 Centos 8 的 Dockerfile  如下所示.
```shell
FROM scratch
ADD centos-8-x86_64.tar.xz /

LABEL org.label-schema.schema-version="1.0" \
    org.label-schema.name="CentOS Base Image" \
    org.label-schema.vendor="CentOS" \
    org.label-schema.license="GPLv2" \
    org.label-schema.build-date="20200611"

CMD ["/bin/bash"]
```


### Dockerfile构建步骤
1. 编写一个 Dockerfile 文件
2. 使用 `docker build` 将其构建成为一个镜像
3. 使用 `docker run` 运行镜像
4. 使用 `docker push` 发布镜像

![ ](Dockerfile介绍及指令说明/Docker-Dockerfile-build-2.jpg)


### Dockerfile常用指令
#### 基础知识
1. 每个保留关键字( 指令 )都必须是大写字母
2. 每一个指令都会创建提交一个新的镜像层 
3. 指令的执行顺序从上到下
4. `#` 表示注释

*在 Docker 中创建镜像最常用的方式就是使用 Dockerfile. 其是一个 Docker 镜像的描述文件，包含了一条条的指令 : 每一条指令构建一层，因此每一条指令的内容就是描述该层应当如何构建. Dockerfile 常用指令如下图所示*

![ ](Dockerfile介绍及指令说明/Docker-Dockerfile-commands.jpg)

* `FROM`         :   基础镜像，一切从这里开始构建
* `MAINTAINER`   :   镜像的作者信息 : 姓名 + 邮箱
* `RUN`          :   镜像构建的时需要运行的命令
* `ADD`          :   将所需文件拷贝到镜像中，例如 nginx，redis等.
* `WORKDIR`      :   指定镜像的工作目录
* `VOLUME`       :   设置挂载目录
* `EXPOSE`       :   暴露端口配置
* `CMD`          :   指定容器启动时要运行的命令，但只有最后一个生效且可被替代
* `ENTRYPOINT`   :   与 CMD 功能相同，区别为其可追加命令
* `ONBUILD`      :   当构建一个被继承 Dockerfile 时这个指令就会被触发执行
* `COPY`         :   与 ADD 指令类似，即可将文件拷贝到镜像中
* `ENV`          :   设置构建时所需的环境变量
* `USER`         :   指定运行用户

---
title: SSH远程连接服务器的两种方式
date: 2020-06-22 19:44:13
tags: [SSH,Linux,Ubuntu]
---

## 简介
*Secure Shell(`SSH`) 是由 IETF(The Internet Engineering Task Force) 制定的建立在应用层基础上的安全网络协议。它是专为远程登录会话( 甚至可以用Windows远程登录Linux服务器进行文件互传 )和其他网络服务提供安全性的协议，可有效弥补网络中的漏洞。通过SSH，可以把所有传输的数据进行加密，也能够防止 DNS 欺骗和 IP 欺骗。还有一个额外的好处就是传输的数据是经过压缩的，所以可以加快传输的速度。目前已经成为Linux系统的标准配置.*


## SSH的安装
1. SSH分为客户端 `openssh-client` 和服务器 `openssh-server`，首先利用以下命令确认电脑上是否安装了客户端和服务器.
```shell
$ dpkg -l | grep ssh

# 如果没有安装则可以通过以下命令进行安装
$ sudo apt-get install openssh-client 
$ sudo apt-get install openssh-server 
```

2. 检查 ssh-server 是否已经启动了.
```shell
$ ps -e | grep ssh

# 如果没有启动可以通过以下命令来启动
$ sudo /etc/init.d/ssh start 
# 停止和重启ssh服务的命令如下所示 
sudo /etc/init.d/ssh stop 
sudo /etc/init.d/ssh restart
```


## SSH远程登录的两种方式
### 方式一 : 口令登录
*命令格式为 : `ssh 客户端用户名@服务器ip地址`*
```shell
$ ssh <remote_username>@<remoteserver.ip>
# 如果客户机的用户名和服务器的用户名相同,登录时可以省略用户名
$ ssh <remoteserver.ip>
# 如果需要调用图形界面程序可以使用 -X 选项
$ ssh -X 123<remote_username>@<remoteserver.ip>
# SSH服务的默认端口是22,我们可以使用 -p 选项来修改端口号
$ ssh -p 123 <remote_username>@<remoteserver.ip>
```


### 方式二 : 公钥登录
*每次登录远程主机都需要输入密码是很不方便，如果想要省去这一步骤，可以利用密钥对进行连接，还可以提高安全性.*
1. 首先在本机生成密钥对
```shell
$ ssh-keygen -t rsa # -t表示类型选项,这里采用 rsa 加密算法
```
> 然后根据提示一步步的按enter键即可( 其中有一个提示是要求设置私钥口令passphrase，不设置则为空。这里看心情吧，如果不放心私钥的安全可以设置一下)，执行结束以后会在 `/home/当前用户目录` 下生成一个 `.ssh` 文件夹,其中包含 `私钥文件 id_rsa` 和 `公钥文件 id_rsa.pub`

#### 将公钥复制到远程主机: 方法 A
2. 将公钥复制到远程主机中( `但由于Windows上没有ssh-copy-id，我们可以使用方法 B` )
```
$ ssh-copy-id <remote_username>@<remoteserver.ip>
```
> 使用 ssh-copy-id 命令将公钥复制到远程主机. ssh-copy-id会将公钥写到远程主机的 `~/.ssh/authorized_key` 文件中. 经过以上两个步骤，以后再登录这个远程主机就不用再输入密码咯 !

3. 测试
```shell
# 再次登录远程服务器时只需输入私钥密码即可.
ssh <remote_username>@<remoteserver.ip>
```

#### 将公钥复制到远程主机: 方法 B
2. 首先将ssh key的公钥上传到远程主机
```shell
$ scp <windows上的公钥> <远程主机用户名>@<远程主机ip或者域名>:~/.ssh/id_rsa.win.pub
# 举例
$ scp C:\Users\<Windows_User>\.ssh\id_rsa.pub <remote_username>@<remoteserver.ip>:~/.ssh/id_rsa.win.pub
```

3. 登录到远程主机，在远程主机上将windows的key添加到authorized_keys文件中.
```shell
# >> : 将id_rsa.win.pub中的内容写入到authorized_keys文件中
$ cat ~/.ssh/id_rsa.win.pub >> ~/.ssh/authorized_keys 
```

4. 测试
```shell
# 再次登录远程服务器时只需输入私钥密码即可.
ssh <remote_username>@<remoteserver.ip>
```


## 参考
* [SSH简介及两种远程登录的方法](https://blog.csdn.net/li528405176/article/details/82810342)
* [如何在Widnows10上用ssh登录Ubuntu](https://xmanyou.com/windows-10-ru-he-zai-widnows10shang-yong-sshdeng-lu-ubuntu/)

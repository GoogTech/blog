---
title: Ubuntu18.04 安装nginx及基本使用
date: 2020-06-21 18:04:20
tags: [Ubuntu,Nginx]
---

*`Nginx`("engine x")是一款是由俄罗斯的程序设计师 Igor Sysoev 所开发高性能的 Web和 反向代理服务器，也是一个 IMAP/POP3/SMTP 代理服务器。在高连接并发的情况下，Nginx是Apache服务器不错的替代品。*


### 安装
```shell
> sudo apt install nginx
```

*安装好的位置*
* `/usr/sbin/nginx` : 主程序
* `/etc/nginx` : 存放配置文件
* `/usr/share/nginx` : 存放静态文件
* `/var/log/nginx` : 存放日志


### 启动并验证效果
*启动并重新加载 nginx 配置文件后，在浏览器输入你的 ip 地址，如果出现Wellcome to nginx那么就是配置成功*
```shell
# 启动nginx
> service nginx start  
# 重新加载nginx配置文件
> service nginx reload  

# 与上面相同的两个命令
> nginx -s reopen # 重启 nginx
> nginx -s stop # 停止 nginx
```


### 查看版本号
```shell
> nginx -v
```


### nginx 配置文件介绍
*> vim /usr/local/webserver/nginx/conf/nginx.conf*

```shell
...     #全局块

events {    #events块
   ...
}

http    #http块
{
    ...     #http全局块
    server      #server块
    { 
        ...     #server全局块
        location [PATTERN]      #location块
        {
            ...
        }
        location [PATTERN] 
        {
            ...
        }
    }
    server
    {
      ...
    }
    ...     #http全局块
}
```

* `全局块` : 配置影响nginx全局的指令。一般有运行nginx服务器的用户组，nginx进程pid存放路径，日志存放路径，配置文件引入，允许生成worker process数等。
* `events块` : 配置影响nginx服务器或与用户的网络连接。有每个进程的最大连接数，选取哪种事件驱动模型处理连接请求，是否允许同时接受多个网路连接，开启多个网络连接序列化等。
* `http块` : 可以嵌套多个server，配置代理，缓存，日志定义等绝大多数功能和第三方模块的配置。如文件引入，mime-type定义，日志自定义，是否使用sendfile传输文件，连接超时时间，单连接请求数等。
* `server块` : 配置虚拟主机的相关参数，一个http中可以有多个server。
* `location块` : 配置请求的路由，以及各种页面的处理情况。


### 验证配置文件的正确性以及重载配置
*修改 nginx 的配置文件后记得验证配置文件的正确性以及重载配置哟*
```shell
# 验证默认配置文件
> nginx -t           
# 重载nginx配置文件
> nginx -s reload     
```


### 参考
> 详情请参考这篇博文( 本文摘自该篇文章 )，写的很详细啦 : [Ubuntu18.04安装Web服务nginx](https://www.cnblogs.com/springsnow/p/12206723.html)

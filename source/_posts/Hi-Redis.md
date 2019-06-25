---
title: Hi Redis ~
date: 2019-06-25 16:13:48
tags: [Redis]
---

## 学习笔记 : 拥抱Redis ~
*简介 : `Redis`(REmote DIctionary Server)是一个非常流行的基于内存的轻量级键值数据库(key-value database). 严格的说与其把Redis称为一种数据库,不如把它称为一种数据服务器更为恰当(`https://matt.sh/thinking-in-redis-part-one`). Redis原生地在内存中实现了多种类型的数据结构,并提供了操作这些数据结构的多种API. 更加重要的是,作为一个需要长期运行的数据库存储服务,Redis还提供了高性能命令处理,高可靠性/扩展性的架构及数据持久化等特性 .*

- *微软开源技术小组(Microsoft Open Technologies group)曾经维护了一个Windows的Redis发行版(😅小哥的联想小新潮中Ubuntu系统好久没玩过了啊,要不先在Windows系统下学习Redis吧, (#→⌒→)都是借口,大懒蛋 ~) : https://github.com/microsoftarchive/redis/releases*


### Windows-Redis安装文件说明
1. *redis.windows.conf          ---redis的配置文件:将redis作为普通软件使用的配置*
2. *redis.windows-service.conf  ---redis的配置文件:将redis作为系统服务的配置*
3. *redis-benchmark.exe         ---测试工具:测试redis的读写性能情况*
4. *redis-check-aof.exe         ---aof 修复检查日志*
5. *redis-cli.exe               ---redis客户端程序*
6. *redis-server.exe            ---redis服务器程序*


### Redis临时服务
1. *进入Redis安装包目录,启动Redis临时服务的命令为 : `redis-server redis.windows.conf`  ,通过该命令可以创建Redis临时服务,既Services列表中不会出现Redis的服务名及其状态,关闭从此窗口既关闭Redis服务.*
```
E:\MS-Redis\Redis
λ redis-server.exe redis.windows.conf
                _._
           _.-``__ ''-._
      _.-``    `.  `_.  ''-._           Redis 3.2.100 (00000000/0) 64 bit
  .-`` .-```.  ```\/    _.,_ ''-._
 (    '      ,       .-`  | `,    )     Running in standalone mode
 |`-._`-...-` __...-.``-._|'` _.-'|     Port: 6379
 |    `-._   `._    /     _.-'    |     PID: 3796
  `-._    `-._  `-./  _.-'    _.-'
 |`-._`-._    `-.__.-'    _.-'_.-'|
 |    `-._`-._        _.-'_.-'    |           http://redis.io
  `-._    `-._`-.__.-'_.-'    _.-'
 |`-._`-._    `-.__.-'    _.-'_.-'|
 |    `-._`-._        _.-'_.-'    |
  `-._    `-._`-.__.-'_.-'    _.-'
      `-._    `-.__.-'    _.-'
          `-._        _.-'
              `-.__.-'

[3796] 25 Jun 17:20:55.403 # Server started, Redis version 3.2.100
[3796] 25 Jun 17:20:55.403 * DB loaded from disk: 0.000 seconds
[3796] 25 Jun 17:20:55.403 * The server is now ready to accept connections on port 6379
```

2. *启动Redis服务后再开启一个`cmder`窗口,运行客户端程序*
```t
E:\MS-Redis\Redis
λ redis-cli.exe # 运行客户端程序的命令
127.0.0.1:6379> set firstKey HelloRedis~
OK
127.0.0.1:6379> get firstKey
"HelloRedis~"
```


### Redis默认服务安装
*进入Redis安装包目录,执行将Redis注册为服务的命令,执行该命令后Services列表中将出现`Redis`服务,但此时该服务并非启动状态,需要执行Redis启动命令来启动服务,启动服务后,即可运行客户端程序哟 ~*
```t
E:\MS-Redis\Redis 
λ redis-server --service-install redis.windows.conf # 将Redis注册为服务的命令

E:\MS-Redis\Redis
λ redis-server --service-start # 启动服务命令
[18704] 25 Jun 17:55:33.957 # Redis service successfully started.

E:\MS-Redis\Redis
λ redis-cli.exe # 运行客户端程序的命令
127.0.0.1:6379> set firstKey helloRedis~
OK
127.0.0.1:6379> get firstKey
"helloRedis~"
127.0.0.1:6379> exit # 退出

E:\MS-Redis\Redis
λ redis-server --service-stop # 停止命令命令
[7784] 25 Jun 18:09:43.467 # Redis service successfully stopped.

E:\MS-Redis\Redis
λ redis-server --service-uninstall # 卸载服务命令
[11968] 25 Jun 18:13:44.417 # Redis service successfully uninstalled.
```


- *参考书籍 : Redis 4.x CookBook ——黄鹏程,王左非 · 著*
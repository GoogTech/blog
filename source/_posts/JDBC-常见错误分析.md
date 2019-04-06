---
title: JDBC 常见错误分析
date: 2019-03-14 19:32:12
tags: [Java,MySQL,JDBC]
---

### JDBC ( Java Database Connectivity ) 常见错误分析

#### 今遇到一个网友问到JDBC中一个常见的错误,让我想起了大一时自己学习JDBC时整理的文档,继而公布出来分享给大家. `根据不同问题给出相应的解析`

### 问题一 : 加载驱动 `Class.forName("com.mysql.jdbc.Driver");`错误
- #### `程序报错信息`
```java
Loading class `com.mysql.jdbc.Driver'. This is deprecated. The new driver class is `com.mysql.cj.jdbc.Driver'. 
The driver is automatically registered via the SPI and manual loading of the driver class is generally unnecessary. ()...

[译文如下]
加载类 'com.mysql.jdbc.Driver' 这是不赞成的.新的驱动程序类是 'com.mysql.cj.jdbc.Driver' 驱动程序.
驱动程序是通过SPI自动注册的，并且手动加载驱动类通常是不必要的...
```
- #### `解析` 
```java
新版的驱动类位置有了变化(不影响使用，但会报警告)
	旧版连接(MySQL Connection/J 5.x) ——> jdbc.url = com.mysql.jdbc.Driver
	新版连接(MySQL Connection/J 6.x) ——> jdbc.url= com.mysql.cj.jdbc.Driver
```
- ### `解决方案`
```java
将 "com.mysql.jdbc.Driver" 改成 "com.mysql.cj.jdbc.Driver" .
```



### 问题二 ：连接错误

- #### `程序报错信息`
```java
SunAug 19 17:39:12 CST 2018 WARN: Establishing SSL connection without server's identity verification is not recommended. According to MySQL 5.5.45+, 5.6.26+ and 5.7.6+ requirements SSL connection must be established by default if explicit option isn't set. For compliance with existing applications not using SSL the verifyServer Certificate property is set to 'false'. You need either to explicitly disable SSL by setting useSSL=false, or set useSSL=true and provide truststore for server certificate verification.

[译文如下]
SunAug 19 17:39∶12 CST 2018警告：不建议建立没有服务器身份验证的SSL连接.根据MySQL 5.5.45 +、5.626+和5.7.6+的要求,如果没有设置显式选项，默认情况下必须建立SSL连接.对于不使用SSL的现有应用程序,'ValuyServer'证书属性设置为'false'.您需要通过设置'USESL=false'来显式禁用'SSL',或者设置'useSSL=true',并为服务器证书验证提供信任存储.
```
- #### `解析以及解决方案`
```java
由报错信息可得知,MySQL版本更新后的新特性: 如果连接数据库必须通过服务器的身份验证,当然上述已给出解决方案的建议.
	方案一 ： 在url后加：useSSL=false; (简单易操作)
	方案二 ： 在url后加：useSSL=true;  (并未服务器证书验证提供信任存储)

```


### 问题三 ：时区错误
- #### `程序报错信息`
```java
Exception in thread "main" java.sql.SQLException: The server time zone value '???ú±ê×??±??' is unrecognized or represents more than one time zone. You must configure either the server or JDBC driver (via the serverTimezone configuration property) to use a more specifc time zone value if you want to utilize time zone support.

[译文如下]
java.sql.SQLException ：服务器时区值 '？？±××？±？' 是不被识别的,或者表示一个以上的时区.如果要利用时区支持,必须配置服务器或JDBC驱动程序(通过 serverTimezone 配置属性)使用更具体的时区值.
```
- #### `解析及解决方案`
```java
为URL添加参数 serverTimezone=UTC 即可,这里的时区可以根据自己数据库的设定来设置（GMT/UTC ）.
```


### 问题四 ：当上述问题全部已经解决后可以正常连接数据库,但是如果重启MySQL服务器后重新连接数据库时发现报错！
- #### `程序报错信息`
```java
java.sql.SQLNonTransientConnectionException: Public Key Retrieval is not allowed.
```

- #### `解决方案`
```java
在URL连接后添加 ： allowPublicKeyRetrieval = true
```


##### `哎哟不知觉又在图书馆又待了一天..都不知已将近九点了,收拾下回寝室啦 ~`

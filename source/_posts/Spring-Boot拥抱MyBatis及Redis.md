---
title: Spring-Boot拥抱MyBatis及Redis ~
date: 2019-07-01 10:26:26
tags: [Spring Boot,MyBatis,Redis]
---

## 学习笔记 : Spring Boot拥抱MyBatis及Redis ~

### Spring Boot集成MyBatis
*下面通过使用`Spring Boot`开发一个基于`SSM`的框架的应用,并通过一个简单的用户信息查询为例,记录如何在Spring Boot中使用`MyBatis` ~*

1. *tb_user.sql : 用户信息数据表*
```sql
create table tb_user
(
    id     int auto_increment primary key,
    name   varchar(15) not null,
    gender char        null,
    email  varchar(50) null
);
```

2. *pom.xml*
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <!-- lookup parent from repository -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.6.RELEASE</version>
        <relativePath/>
    </parent>
    <groupId>pers.huangyuhui</groupId>
    <artifactId>redis</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>springboot_redis</name>
    <description>Demo project for Spring Boot</description>

    <properties>
        <java.version>11</java.version>
    </properties>

    <dependencies>
        <!-- Web -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <!-- MyBatis -->
        <dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
            <version>2.0.1</version>
        </dependency>
        <!-- Redis -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-redis</artifactId>
        </dependency>
        <!-- MySQL -->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <scope>runtime</scope>
        </dependency>
    </dependencies>

    <build>
        <!-- mvn package -->
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

</project>
```

3. *SpringbootRedisApplication.java : Spring Boot启动类*
```java
package pers.huangyuhui.redis;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class SpringbootRedisApplication {

    public static void main(String[] args) {
        SpringApplication.run(SpringbootRedisApplication.class, args);
    }

}
```

4. *application.properties : Spring Boot配置文件*
```t
#JDBC-Connection Configuration Information
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.datasource.url=jdbc:mysql://localhost:3306/test?useSSL=false&serverTimezone=UTC&allowPublicKeyRetrieval=true
spring.datasource.username=xxxxxx
spring.datasource.password=xxxxxx
#Logging Configuration Information
logging.level.pers.huangyuhui.redis=debug
```

5. *User.java : 用户信息实体类*
```java
package pers.huangyuhui.redis.bean;

/**
 * @project: springboot_redis
 * @description: 用户信息
 * @author: 黄宇辉
 * @date: 7/1/2019-7:08 AM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public class User {

    private Integer id;
    private String name;
    private String gender;
    private String email;

    //getter and setter
    ······
}
```

6. *UserMapper.java : 操控用户数据表信息*
```java
package pers.huangyuhui.redis.dao;

import org.apache.ibatis.annotations.Mapper;
import org.apache.ibatis.annotations.Select;
import pers.huangyuhui.redis.bean.User;

import java.util.List;

/**
 * @project: springboot_redis
 * @description: Dao层-操控用户信息
 * @author: 黄宇辉
 * @date: 7/1/2019-7:12 AM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
@Mapper
public interface UserMapper {

    // TODO: 7/1/2019 查询所有用户信息
    @Select("select * from tb_user")
    List<User> selectAll();

    // TODO: 7/1/2019 根据id删除指定用户信息
    @Delete("Delete from tb_user where id = #{id}")
    void deleteById(Integer id);
}
```

7. *UserService.java : 操控用户信息*
```java
package pers.huangyuhui.redis.service;

import pers.huangyuhui.redis.bean.User;

import java.util.List;

/**
 * @project: springboot_redis
 * @description: Service层-操控用户信息
 * @author: 黄宇辉
 * @date: 7/1/2019-7:25 AM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public interface UserService {

    // TODO: 7/1/2019 获取或有用户信息
    List<User> findAll();

     // TODO: 7/1/2019 根据id删除指定用户信息
    void delete(Integer id);
}
```

8. *UserServiceImpl.java : UserService.java的实现类*
```java
package pers.huangyuhui.redis.service.impl;

import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;
import pers.huangyuhui.redis.bean.User;
import pers.huangyuhui.redis.dao.UserMapper;
import pers.huangyuhui.redis.service.UserService;

import javax.annotation.Resource;
import java.util.List;

/**
 * @project: springboot_redis
 * @description: UserService的实现类
 * @author: 黄宇辉
 * @date: 7/1/2019-7:27 AM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
@Service
@Transactional
public class UserServiceImpl implements UserService {

    //注入Mapper对象
    @Resource
    private UserMapper userMapper;

    @Override
    public List<User> findAll() {
        return userMapper.selectAll();
    }

    @Override
    public void delete(Integer id) {
        userMapper.deleteById(id);
    }

}
```
 
9. *UserController.java : 用户信息控制器*
```java
package pers.huangyuhui.redis.controller;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;
import pers.huangyuhui.redis.bean.User;
import pers.huangyuhui.redis.service.UserService;

import java.util.List;

/**
 * @project: springboot_redis
 * @description: 用户控制器
 * @author: 黄宇辉
 * @date: 7/1/2019-7:28 AM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
@Controller
@RequestMapping("/user")
public class UserController {

    //注入业务对象
    @Autowired
    private UserService userService;

    /**
     * @description: 获取所有用户信息
     * @date: 2019-07-01 8:16 AM
     * @return: java.util.List<pers.huangyuhui.redis.bean.User>
     */
    @RequestMapping("/getUserList")
    @ResponseBody
    public List<User> getUserList() { return userService.findAll(); }

    /**
     * @description: 删除指定id的好友信息
     * @param: id
     * @date: 2019-07-01 12:51 PM
     * @return: void
     */
    @RequestMapping("/deleteUser/{id}")
    public void deleteUser(@PathVariable Integer id) { userService.delete(id); }

}
```

10. *userList.html : 用户信息列表页面 (将EasyUI框架的资源文件拷贝到src/main/resource下的static文件夹下,并在static文件夹中创建`userList.html`页面文件)*
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>用户信息列表页面</title>
    <!-- CSS -->
    <link rel="stylesheet" type="text/css" href="easyui/themes/metro/easyui.css"/>
    <!-- JS -->
    <script type="text/javascript" src="easyui/jquery.min.js"></script>
    <script type="text/javascript" src="easyui/jquery.easyui.min.js"></script>
    <script type="text/javascript" src="easyui/themes/locale/easyui-lang-zh_CN.js"></script>

    <script type="text/javascript">
        $(function () {
            $('#graid').datagrid({
                url: 'user/getUserList',
                fit: true,
                columns: [[
                    {field: 'id', title: '编号', width: 100},
                    {field: 'name', title: '姓名', width: 100},
                    {field: 'gender', title: '性别', width: 100},
                    {field: 'email', title: '邮箱', width: 200}
                ]]
            });
        });
    </script>
</head>
<body>
<!-- 用户信息列表 -->
<table id="graid"></table>
</body>
</html>
```

11.  *启动Spring Boot项目,在浏览器中输入访问地址 : `http://localhost:8080/userList.html`后,显示效果参考下图 :*
![ ](Spring-Boot拥抱MyBatis及Redis/UserListView.PNG)

*紧接着刷新页面三次,其控制台打印的日志信息如下所示,发现每刷新一次页面都会执行一次查询数据库操作,`但如果将列表数据缓冲到Redis后,会发现控制台只会出现一次查询数据库的日志信息哟 !`*
```
2019-07-01 11:41:49.868 DEBUG 7844 --- [nio-8080-exec-3] p.h.redis.dao.UserMapper.selectAll       : ==>  Preparing: select * from tb_user 
2019-07-01 11:41:49.869 DEBUG 7844 --- [nio-8080-exec-3] p.h.redis.dao.UserMapper.selectAll       : ==> Parameters: 
2019-07-01 11:41:49.871 DEBUG 7844 --- [nio-8080-exec-3] p.h.redis.dao.UserMapper.selectAll       : <==      Total: 5

2019-07-01 11:41:54.250 DEBUG 7844 --- [nio-8080-exec-5] p.h.redis.dao.UserMapper.selectAll       : ==>  Preparing: select * from tb_user 
2019-07-01 11:41:54.250 DEBUG 7844 --- [nio-8080-exec-5] p.h.redis.dao.UserMapper.selectAll       : ==> Parameters: 
2019-07-01 11:41:54.253 DEBUG 7844 --- [nio-8080-exec-5] p.h.redis.dao.UserMapper.selectAll       : <==      Total: 5

2019-07-01 11:41:57.251 DEBUG 7844 --- [nio-8080-exec-7] p.h.redis.dao.UserMapper.selectAll       : ==>  Preparing: select * from tb_user 
2019-07-01 11:41:57.251 DEBUG 7844 --- [nio-8080-exec-7] p.h.redis.dao.UserMapper.selectAll       : ==> Parameters: 
2019-07-01 11:41:57.256 DEBUG 7844 --- [nio-8080-exec-7] p.h.redis.dao.UserMapper.selectAll       : <==      Total: 5

Process finished with exit code -1
```



### Spring Boot集成Redis
*Redis是一个完全开源的,遵守BSD协议的,内存中的数据结构存储,它既可以作为`数据库`,也可以作为`缓存`和`消息代理`. 通常在企业中常将其作为缓存使用哟~ 接下来在Spring Boot集成MyBatis示例程序基础上将列表数据缓存到Redis中,详细步骤如下 :*

1. *在`pom.xml`中添加Spring Boot支持Redis的依赖*
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```
2. *在`SpringbootRedisApplication.java`中添加`@EnableCaching`注解来开启缓存*
```java
@SpringBootApplication
@EnableCaching
public class SpringbootRedisApplication {
    //······
}
```

3. *在业务逻辑类`UserServiceImpl.java`的方法上添加`@Cacheable`注解来支持Redis缓存*
```java
@Override
@Cacheable(value = "UserCatch", key = "'user.selectAll'")
public List<User> findAll() {
    return userMapper.selectAll();
}
```

4. *为便于数据的传输,将实体类`User.java`实现序列化接口Serializable*
```java
public class User implements Serializable {
    //······
}
```

5. *在`application.properties`中指定`Redis`缓存主机地址及端口号. 我使用的是Windows的Redis发行版哟,其学习笔记 : https://yubuntu0109.github.io/2019/06/25/Hi-Redis/*
```t
#Redis Configuration Infromation
spring.redis.host=localhost
spring.redis.port=6379
```

6. *启动Redis服务,并启动Spring Boot项目,在浏览器地址栏中输入访问地址 : `http://localhost:8080/userList.html`后,不断刷新页面,其控制台打印的日志信息如下,结论 : 在没有使用Redis缓存之前,每刷新一次页面,都会执行一次查询数据库的操作,添加缓存后,会发现日志信息中只出现了一次查询语句,这也就说明所配置的Redis缓存已经生效啦 ~*
```
2019-07-01 12:43:06.945  INFO 1984 --- [nio-8080-exec-1] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring DispatcherServlet 'dispatcherServlet'

2019-07-01 12:43:06.946  INFO 1984 --- [nio-8080-exec-1] o.s.web.servlet.DispatcherServlet        : Initializing Servlet 'dispatcherServlet'

2019-07-01 12:43:06.951  INFO 1984 --- [nio-8080-exec-1] o.s.web.servlet.DispatcherServlet        : Completed initialization in 5 ms

2019-07-01 12:43:07.218  INFO 1984 --- [nio-8080-exec-7] io.lettuce.core.EpollProvider            : Starting without optional epoll library

2019-07-01 12:43:07.218  INFO 1984 --- [nio-8080-exec-7] io.lettuce.core.KqueueProvider           : Starting without optional kqueue library

2019-07-01 12:43:07.701  INFO 1984 --- [nio-8080-exec-7] com.zaxxer.hikari.HikariDataSource       : HikariPool-1 - Starting...
2019-07-01 12:43:07.791  INFO 1984 --- [nio-8080-exec-7] com.zaxxer.hikari.HikariDataSource       : HikariPool-1 - Start completed.

2019-07-01 12:43:07.805 DEBUG 1984 --- [nio-8080-exec-7] p.h.redis.dao.UserMapper.selectAll       : ==>  Preparing: select * from tb_user 
2019-07-01 12:43:07.815 DEBUG 1984 --- [nio-8080-exec-7] p.h.redis.dao.UserMapper.selectAll       : ==> Parameters: 
2019-07-01 12:43:07.836 DEBUG 1984 --- [nio-8080-exec-7] p.h.redis.dao.UserMapper.selectAll       : <==      Total: 7


Process finished with exit code -1
```


#### 清除Redis缓存
*当执行添加,更新和删除操作后,数据库中的数据会发生变化,继而Redis缓存中的数据同样也需要进行相应的变化,为了保证Redis缓存中的数据与数据库中的一致,通常需要在执行`添加`,`更新`和`删除`操作之前清除缓存,然后再下一次执行查询操作时,将新的数据存储到Redis缓存中 .*

*若要实现清除缓存的功能,只需在相应的方法中使用`@CacheEvict`注解即可,下面以案例程序中的删除用户功能为例,在用户业务逻辑类的`deleteUser()`方法上添加`@CacheEvict`注解 :*
```java
@Override
@CacheEvict(value = "UserCatch", key = "'user.selectAll'")
public void delete(Integer id) {
    userMapper.deleteById(id);
}
```

*重启Spring Boot项目,在浏览器地址栏中输入访问地址 : `http://localhost:8080/user/deleteUser/5`, `../6` , `../7`删除id为5,6,7的用户信息后再查询用户列表信息 : `http://localhost:8080/userList.html`,其控制台打印的日志信息如下 :*
```
2019-07-01 18:13:45.143 DEBUG 14764 --- [nio-8080-exec-1] p.h.redis.dao.UserMapper.deleteById      : ==>  Preparing: Delete from tb_user where id = ? 
2019-07-01 18:13:45.160 DEBUG 14764 --- [nio-8080-exec-1] p.h.redis.dao.UserMapper.deleteById      : ==> Parameters: 5(Integer)
2019-07-01 18:13:45.162 DEBUG 14764 --- [nio-8080-exec-1] p.h.redis.dao.UserMapper.deleteById      : <==    Updates: 1

2019-07-01 18:13:45.331  INFO 14764 --- [nio-8080-exec-1] io.lettuce.core.EpollProvider            : Starting without optional epoll library
2019-07-01 18:13:45.332  INFO 14764 --- [nio-8080-exec-1] io.lettuce.core.KqueueProvider           : Starting without optional kqueue library

2019-07-01 18:15:16.033 DEBUG 14764 --- [nio-8080-exec-4] p.h.redis.dao.UserMapper.deleteById      : ==>  Preparing: Delete from tb_user where id = ? 
2019-07-01 18:15:16.033 DEBUG 14764 --- [nio-8080-exec-4] p.h.redis.dao.UserMapper.deleteById      : ==> Parameters: 6(Integer)
2019-07-01 18:15:16.034 DEBUG 14764 --- [nio-8080-exec-4] p.h.redis.dao.UserMapper.deleteById      : <==    Updates: 1

2019-07-01 18:15:31.217 DEBUG 14764 --- [nio-8080-exec-6] p.h.redis.dao.UserMapper.deleteById      : ==>  Preparing: Delete from tb_user where id = ? 
2019-07-01 18:15:31.218 DEBUG 14764 --- [nio-8080-exec-6] p.h.redis.dao.UserMapper.deleteById      : ==> Parameters: 7(Integer)
2019-07-01 18:15:31.219 DEBUG 14764 --- [nio-8080-exec-6] p.h.redis.dao.UserMapper.deleteById      : <==    Updates: 1

2019-07-01 18:15:47.353 DEBUG 14764 --- [nio-8080-exec-4] p.h.redis.dao.UserMapper.selectAll       : ==>  Preparing: Select * from tb_user 
2019-07-01 18:15:47.353 DEBUG 14764 --- [nio-8080-exec-4] p.h.redis.dao.UserMapper.selectAll       : ==> Parameters: 
2019-07-01 18:15:47.377 DEBUG 14764 --- [nio-8080-exec-4] p.h.redis.dao.UserMapper.selectAll       : <==      Total: 4

Process finished with exit code -1
```

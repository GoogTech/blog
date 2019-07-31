---
title: Hi Shiro ~
date: 2019-07-31 14:23:55
tags: [Java,Shiro]
---

## 学习笔记 : 初识Shiro ~
*简介 : `Apache Shiro`是一个强大易用的Java安全框架,提供了认证、授权、加密和会话管理等功能，对于任何一个应用程序,Shiro都可以提供全面的安全管理服务*

*权限管理包括用户`身份认证`和`授权`两个部分,简称认证授权*
1. *身份认证 : 为判断用户是否为合法用户的过程,最常用的身份认证方式就是系统通过核对用户输入的用户名和口令,将其与系统中存储的该用户信息相对比,继而来判断用户身份是否正确*
2. *授权 : 既访问权限,控制用户访问资源的权限. 主体(`subject`)进行身份认证后需要分配权限方可访问系统的资源*


### 架构解析
*Shiro可以非常容易的开发出足够好的应用,其不仅可以用在JavaSE环境,也可以用在JavaEE环境. Shiro可以帮助我们完成 : 认证、授权、加密、会话管理、与Web集成、缓存等. 其基本功能点及其解析如下所示 :*

![](Hi-Shiro\shiro-framework-api.png)

* `Authentication` : 身份认证 / 登录,验证用户是不是拥有相应的身份
* `Authorization` : 授权,即权限验证,验证某个已认证的用户是否拥有某个权限. 即判断用户是否能做事情,常见的如 : 验证某个用户是否拥有某个角色,或者细粒度的验证某个用户对某个资源是否具有某个权限
* `Session Manager` : 会话管理,即用户登录后就是一次会话,在没有退出之前,它的所有信息都在会话中. 会话可以是普通JavaSE环境的,也可以是如Web环境的
* `Cryptography` : 加密,保护数据的安全性,如密码加密存储到数据库,而不是明文存储
* `Web Support` : Web支持,可以非常容易的集成到Web环境中
* `Caching` : 缓存,比如用户登录后,其用户信息、拥有的角色 / 权限不必每次去查,这样可以提高效率
* `Concurrency` : shiro支持多线程应用的并发验证,即如在一个线程中开启另一个线程,能把权限自动传播过去
* `Testing` : 提供测试支持
* `Run As` : 允许一个用户假装为另一个用户(在他们允许的情况下)的身份进行访问
* `Remember Me` : 记住我,这个是非常常见的功能,即一次登录后,下次不用重复登录了


#### 外部结构
*外部架构图,即从应用程序角度的来观察如何使用Shiro完成工作 :*

![](Hi-Shiro\Shiro-外部架构图.png)

*可以看到,应用代码直接交互的对象是`Subject`,也就是说Shiro的对外API核心就是Subject. 其每个API的含义如下所示 :*
* `Subject` : 主体,代表了当前"用户",这个用户不一定是一个具体的人,与当前应用交互的任何东西都是Subject,如网络爬虫,机器人等,即为一个抽象概念. 所有Subject都绑定到SecurityManager,与Subject的所有交互都会委托给SecurityManager. 可以把Subject认为是一个门面,SecurityManager才是实际的执行者
* `SecurityManager` : 安全管理器,即所有与安全有关的操作都会与SecurityManager交互,且它管理着所有Subject. 可以看出它是Shiro的核心,它负责与后边介绍的其他组件进行交互,如果学习过SpringMVC,你可以把它看成`DispatcherServlet`前端控制器
* `Realm` : 域,Shiro从Realm获取安全数据(如用户、角色、权限),就是说SecurityManager要验证用户身份,那么它需要从Realm获取相应的用户进行比较以确定用户身份是否合法,也需要从Realm得到用户相应的角色 / 权限进行验证用户是否能进行操作. 可以把Realm看成 DataSource,即安全数据源

*也就是说,最简单的一个Shiro应用可以基本分为以下两个步骤 :*
1. *应用代码通过Subject来进行认证和授权,而Subject又将所有的互交都委托给了SecurityManager*
2. *我们需要给Shiro的SecurityManager注入Realm,从而让SecurityManager能得到合法的用户及其权限进行判断*
   
*从以上也可以看出,Shiro不提供维护用户 / 权限,而是通过Realm让开发人员自己注入*


#### 内部结构
*接下来我们来从Shiro内部来看下Shiro的架构,如下图所示 :*

![](Hi-Shiro\Shiro-内部结构图.png)

* `Subject` : 主体,可以看到主体可以是任何可以与应用交互的"用户"
* `SecurityManager` : 相当于SpringMVC中的`DispatcherServlet`或Struts2中的FilterDispatcher; 是Shiro的心脏. 所有具体的交互都通过SecurityManager进行控制,它管理着所有Subject、且负责进行认证和授权、及会话、缓存的管理
* `Authenticator` : 认证器,负责主体认证的,这是一个扩展点,如果用户觉得Shiro默认的不好,可以自定义实现. 其需要认证策略(Authentication Strategy),即什么情况下算用户认证通过了
* `Authrizer` : 授权器,或者访问控制器,用来决定主体是否有权限进行相应的操作,即控制着用户能访问应用中的哪些功能
* `Realm` : 可以有1个或多个Realm,可以认为是安全实体数据源,即用于获取安全实体. 可以是JDBC实现,也可以是LDAP实现,或者内存实现等,由用户提供. 注意 : Shiro不知道你的用户 / 权限存储在哪及以何种格式存储,所以我们一般在应用中都需要实现自己的Realm
* `SessionManager` : 如果写过Servlet就应该知道Session的概念,Session需要有人去管理它的生命周期,这个组件就是SessionManager,而Shiro并不仅仅可以用在Web环境,也可以用在如普通的JavaSE环境、EJB等环境. 所以Shiro就抽象了一个自己的Session来管理主体与应用之间交互的数据. 这样的话,比如我们在Web环境用,刚开始是一台Web服务器,接着又上了台EJB服务器,这时想把两台服务器的会话数据放到一个地方,这个时候就可以实现自己的分布式会话(如把数据放到Memcached服务器)
* `SessionDAO` : DAO大家都用过,数据访问对象,用于会话的CRUD,比如我们想把Session保存到数据库,那么可以实现自己的 SessionDAO,通过如JDBC写到数据库. 比如想把Session放到Memcached中,可以实现自己的Memcached SessionDAO. 另外SessionDAO中可以使用Cache进行缓存,以提高性能
* `CacheManager` : 缓存控制器,来管理如用户、角色、权限等的缓存的. 因为这些数据基本上很少去改变,放到缓存中后可以提高访问的性能
* `Cryptography` : 密码模块,Shiro提高了一些常见的加密组件用于如密码加密 / 解密



### 案例
#### 第一个Shiro程序
*接下来通过一个简单的用户身份验证程序,来感受一下Shiro的魅力吧 !*
1. *pom.xml : Maven依赖*
```xml
<dependencies>
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.11</version>
        <scope>test</scope>
    </dependency>
    <!-- Shiro核心包 -->
    <dependency>
        <groupId>org.apache.shiro</groupId>
        <artifactId>shiro-core</artifactId>
        <version>1.3.2</version>
    </dependency>
    <!-- slf4j的接口实现 -->
    <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-log4j12</artifactId>
        <version>1.7.12</version>
    </dependency>
</dependencies>
```

2. *log4j.properties : 日志配置信息*
```t
log4j.rootLogger=INFO, stdout
log4j.appender.stdout=org.apache.log4j.ConsoleAppender
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern=%d %p [%c] - %m %n
# General Apache libraries
log4j.logger.org.apache=WARN
# Spring
log4j.logger.org.springframework=WARN
# Default Shiro logging
log4j.logger.org.apache.shiro=TRACE
# Disable verbose logging
log4j.logger.org.apache.shiro.util.ThreadContext=WARN
log4j.logger.org.apache.shiro.cache.ehcache.EhCache=WARN
```

3. *shiro.ini : 存储用户身份信息(账户=密码)*
```ini
[users]
root=yubuntu0109
```

4. *ShiroTest.java : 验证用户登录信息*
```java
package pers.huangyuhui;

import org.apache.shiro.SecurityUtils;
import org.apache.shiro.authc.AuthenticationException;
import org.apache.shiro.authc.IncorrectCredentialsException;
import org.apache.shiro.authc.UnknownAccountException;
import org.apache.shiro.authc.UsernamePasswordToken;
import org.apache.shiro.config.IniSecurityManagerFactory;
import org.apache.shiro.mgt.SecurityManager;
import org.apache.shiro.subject.Subject;
import org.apache.shiro.util.Factory;
import org.junit.Test;

/**
 * Shiro认证测试:验证用户登录信息
 */
public class ShiroTest {

    @Test
    public void testLogin() {
        //1:加载配置文件,创建SecurityManager工厂对象
        Factory<SecurityManager> factory = new IniSecurityManagerFactory("classpath:shiro.ini");
        //2:获得securityManager实例对象
        SecurityManager securityManager = factory.getInstance();
        //3:将securityManger实例绑定到当前运行环境中,便于访问
        SecurityUtils.setSecurityManager(securityManager);
        //4:创建当前登录的主体
        Subject currentUser = SecurityUtils.getSubject();
        //5:绑定主体登录的身份/凭证,既账户及密码
        UsernamePasswordToken token = new UsernamePasswordToken("root", "yubuntu0109");
        //6:主体登录
        try {
            currentUser.login(token);
            System.out.println("用户身份是否验证成功 :" + currentUser.isAuthenticated());
        } catch (UnknownAccountException e) {
            System.err.println("账户信息错误 !");
        } catch (IncorrectCredentialsException e) {
            System.err.println("密码信息错误 !");
        } catch (AuthenticationException e) {
            e.printStackTrace();
        }
        //8:注销登录
        currentUser.logout();
        System.out.println("身份身份是否注销成功 :" + !currentUser.isAuthenticated());

    }
}
```

5. *程序运行结果如下所示 :*
```java
// ·····
用户身份是否验证成功 :true
// ·····
身份身份是否注销成功 :true
```


#### 官方入门案例
1. *pom.xml : Maven依赖(同上)*

2. *log4.properties : 日志配置信息(同上)*

3. *shiro.ini : 存储具有指定权限的,不同身份的用户信息*
```ini
# =============================================================================
# Quickstart INI Realm configuration
#
# For those that might not understand the references in this file, the
# definitions are all based on the classic Mel Brooks' film "Spaceballs". ;)
# =============================================================================

# -----------------------------------------------------------------------------
# Users and their assigned roles
#
# Each line conforms to the format defined in the
# org.apache.shiro.realm.text.TextConfigurationRealm#setUserDefinitions JavaDoc
# -----------------------------------------------------------------------------
[users]
# user 'root' with password 'secret' and the 'admin' role
root = secret, admin
# user 'guest' with the password 'guest' and the 'guest' role
guest = guest, guest
# user 'presidentskroob' with password '12345' ("That's the same combination on
# my luggage!!!" ;)), and role 'president'
presidentskroob = 12345, president
# user 'darkhelmet' with password 'ludicrousspeed' and roles 'darklord' and 'schwartz'
darkhelmet = ludicrousspeed, darklord, schwartz
# user 'lonestarr' with password 'vespa' and roles 'goodguy' and 'schwartz'
lonestarr = vespa, goodguy, schwartz

# -----------------------------------------------------------------------------
# Roles with assigned permissions
# 
# Each line conforms to the format defined in the
# org.apache.shiro.realm.text.TextConfigurationRealm#setRoleDefinitions JavaDoc
# -----------------------------------------------------------------------------
[roles]
# 'admin' role has all permissions, indicated by the wildcard '*'
admin = *
# The 'schwartz' role can do anything (*) with any lightsaber:
schwartz = lightsaber:*
# The 'goodguy' role is allowed to 'drive' (action) the winnebago (type) with
# license plate 'eagle5' (instance specific id)
goodguy = winnebago:drive:eagle5
```

4. *Quickstart.java : 一个简单的入门级程序,教你如何使用Shiro的API*
```java
package pers.huangyuhui;

import org.apache.shiro.SecurityUtils;
import org.apache.shiro.authc.*;
import org.apache.shiro.config.IniSecurityManagerFactory;
import org.apache.shiro.mgt.SecurityManager;
import org.apache.shiro.session.Session;
import org.apache.shiro.subject.Subject;
import org.apache.shiro.util.Factory;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

/**
 * Simple Quickstart application showing how to use Shiro's API.
 *
 * @since 0.9 RC2
 */
public class Quickstart {

    private static final transient Logger log = LoggerFactory.getLogger(Quickstart.class);

    public static void main(String[] args) {

        // The easiest way to create a Shiro SecurityManager with configured
        // realms, users, roles and permissions is to use the simple INI config.
        // We'll do that by using a factory that can ingest a .ini file and
        // return a SecurityManager instance:

        // Use the shiro.ini file at the root of the classpath
        // (file: and url: prefixes load from files and urls respectively):
        Factory<SecurityManager> factory = new IniSecurityManagerFactory("classpath:shiro.ini");
        SecurityManager securityManager = factory.getInstance();

        // for this simple example quickstart, make the SecurityManager
        // accessible as a JVM singleton.  Most applications wouldn't do this
        // and instead rely on their container configuration or web.xml for
        // webapps.  That is outside the scope of this simple quickstart, so
        // we'll just do the bare minimum so you can continue to get a feel
        // for things.
        SecurityUtils.setSecurityManager(securityManager);

        // Now that a simple Shiro environment is set up, let's see what you can do:

        // get the currently executing user:
        Subject currentUser = SecurityUtils.getSubject();

        // Do some stuff with a Session (no need for a web or EJB container!!!)
        Session session = currentUser.getSession();
        session.setAttribute("someKey", "aValue");
        String value = (String) session.getAttribute("someKey");
        if (value.equals("aValue")) {
            log.info("Retrieved the correct value! [" + value + "]");
        }

        // let's login the current user so we can check against roles and permissions:
        if (!currentUser.isAuthenticated()) {
            UsernamePasswordToken token = new UsernamePasswordToken("lonestarr", "vespa");
            token.setRememberMe(true);
            try {
                currentUser.login(token);
            } catch (UnknownAccountException uae) {
                log.info("There is no user with username of " + token.getPrincipal());
            } catch (IncorrectCredentialsException ice) {
                log.info("Password for account " + token.getPrincipal() + " was incorrect!");
            } catch (LockedAccountException lae) {
                log.info("The account for username " + token.getPrincipal() + " is locked.  " +
                        "Please contact your administrator to unlock it.");
            }
            // ... catch more exceptions here (maybe custom ones specific to your application?
            catch (AuthenticationException ae) {
                //unexpected condition?  error?
            }
        }

        //say who they are:
        //print their identifying principal (in this case, a username):
        log.info("User [" + currentUser.getPrincipal() + "] logged in successfully.");

        //test a role:
        if (currentUser.hasRole("schwartz")) {
            log.info("May the Schwartz be with you!");
        } else {
            log.info("Hello, mere mortal.");
        }

        //test a typed permission (not instance-level)
        if (currentUser.isPermitted("lightsaber:wield")) {
            log.info("You may use a lightsaber ring.  Use it wisely.");
        } else {
            log.info("Sorry, lightsaber rings are for schwartz masters only.");
        }

        //a (very powerful) Instance Level permission:
        if (currentUser.isPermitted("winnebago:drive:eagle5")) {
            log.info("You are permitted to 'drive' the winnebago with license plate (id) 'eagle5'.  " +
                    "Here are the keys - have fun!");
        } else {
            log.info("Sorry, you aren't allowed to drive the 'eagle5' winnebago!");
        }

        //all done - log out!
        currentUser.logout();

        System.exit(0);
    }
}
```
---
title: 详解Shiro登录登出的操作流程
date: 2019-08-01 13:48:07
tags: [Java,Shiro]
---

## 学习笔记 : 详解Shiro登录登出的操作流程
*在shiro中,用户需要提供principals(身份)和credentials(证明)给shiro,继而来验证用户的身份信息,最常见的princpals和 credentials组合就是用户名 / 密码啦~*

* principals : 身份,即主体的标识属性,如用户名、邮箱等,需唯一. 一个主体可以有多个principals,但只有一个Primary principals,一般是用户名 / 密码 / 手机号
* credentials : 证明 / 凭证,即只有主体知道的安全值,如密码 / 数字证书等


### 登录登出案例
*接下来通过一个模拟验证用户登录信息的示例程序,来探究并分析一下Shiro的验证用户身份信息的流程*

1. *pom.xml : Maven依赖*
```xml
<dependencies>
    <!--Junit 单元测试 -->
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

2. *shiro.ini : 存储用户身份信息(账户=密码)*
```ini
[users]
root=yubuntu0109
```

3. *ShiroTest.java : 模拟验证用户登录信息(把断点打在`currentUser.login(token)`)*
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
            System.err.println("用户账户信息错误 !");
        } catch (IncorrectCredentialsException e) {
            System.err.println("用户密码信息错误 !");
        } catch (AuthenticationException e) {
            e.printStackTrace();
        }
        //8:注销登录
        currentUser.logout();
        System.out.println("身份身份是否注销成功 :" + !currentUser.isAuthenticated());

    }
}
```

4. *程序运行效果如下所示 :*
```java
// ······
用户身份是否验证成功 :true
// ······
身份身份是否注销成功 :true
```


#### 案例源码流程图
*在分析源码之前,先看一下该案例的源码流程图吧,也便于后面对源码分析的理解~*
![](详解Shiro登录登出的操作流程\Shiro-登录登出源码流程图.png)

1. *调用`subject.login(AuthenticationToken token)`方法进行用户登录,其会自动委托给`securityManager.login(Subject subject, AuthenticationToken token)`方法进行登录*
2. *`securityManager`(安全管理器)通过`Authenticator`(认证器)进行认证*
3. *`Authenticator`的实现类`ModularRealmAuthenticator`通过调用`realm`从`shiro.ini`配置文件中获取用户真实的信息(账户和密码),这里的Realm(域)可以看成DataSource,即安全数据源*
4. *`IniRealm`(可通过加载.ini文件生成reaml对象)先根据`token`中的账号去`shiro.ini`配置文件中去匹配该账号,如果找不到则`ModularRealmAuthenticator`返回null,如果找到则继续匹配密码,若匹配成功则认证通过,反之不通过哟~* 
5. *最后可以使用`Subject.logout()`进行退出操作*


### 案例源码流程分析
1. *运行上述程序,首先debug(Step into)进入`currentUser.login(token)`内部,既DelegatingSubject.java. 从源码可以看出 : 与Subject的所有交互都会委托给SecurityManager. 可以把Subject认为是一个门面,SecurityManager才是实际的执行者哟~*
```java
public void login(AuthenticationToken token) throws AuthenticationException {
        this.clearRunAsIdentitiesInternal();
        Subject subject = this.securityManager.login(this, token);
        // ······
```

*继续(Step into)进入`this.securityManager.login(this, token);`,既DefaultSecurityManager.java. 此时验证用户身份的的操作正式开始*
```java
public Subject login(Subject subject, AuthenticationToken token) throws AuthenticationException {
        AuthenticationInfo info;
        try {
            info = this.authenticate(token);
        } catch (AuthenticationException var7) {
        // ······

```

2. *继续(Step into)进入`this.authenticate(token)`,既AuthenticatingSecurityManager.java. 从源码可以看出SecurityManager将`token`委托给了Authenticator(认证器)去执行用户身份的认证操作*
```java
public AuthenticationInfo authenticate(AuthenticationToken token) throws AuthenticationException {
        return this.authenticator.authenticate(token);
    }
```

3. *继续(Step into)进入`this.authenticator.authenticate(token)`,既AbstractAuthenticator.java. 由`info = this.doAuthenticate(token);`获取一个AuthenticationInfo对象*
```java
 public final AuthenticationInfo authenticate(AuthenticationToken token) throws AuthenticationException {
        if (token == null) {
            throw new IllegalArgumentException("Method argument (authentication token) cannot be null.");
        } else {
            log.trace("Authentication attempt received for token [{}]", token);

            AuthenticationInfo info;
            try {
                info = this.doAuthenticate(token);
                if (info == null) {
                    String msg = "No account information found for authentication token [" + token + "] by this Authenticator instance.  Please check that it is configured correctly.";
                    throw new AuthenticationException(msg);
                }
        // ······
```

4. *继续(Step into)进入`info = this.doAuthenticate(token)`,既ModularRealmAuthenticator.java. 然后从系统所加载配置文件所对应的Realms的值中获取(`this.getRealms()`)一个realms对象,该对象中包含一个`users`属性,其中包含着用户的身份及凭证信息,例如账号为root,密码为yubuntu0109的用户信息*
```java
 protected AuthenticationInfo doAuthenticate(AuthenticationToken authenticationToken) throws AuthenticationException {
        assertRealmsConfigured();
        Collection<Realm> realms = getRealms();
        if (realms.size() == 1) {
            return doSingleRealmAuthentication(realms.iterator().next(), authenticationToken);
        } else {
            return doMultiRealmAuthentication(realms, authenticationToken);
        }
    }
    // ······
```

*继续(Step into)进入`this.doSingleRealmAuthentication((Realm)realms.iterator().next(), authenticationToken)`. 发现其通过`realm.getAuthenticationInfo(token)`获取一个AuthenticationInfo对象(其中存储着从Realm中获取的用户身份信息)*
```java
protected AuthenticationInfo doSingleRealmAuthentication(Realm realm, AuthenticationToken token) {
        if (!realm.supports(token)) {
            String msg = "Realm [" + realm + "] does not support authentication token [" +
                    token + "].  Please ensure that the appropriate Realm implementation is " +
                    "configured correctly or that the realm accepts AuthenticationTokens of this type.";
            throw new UnsupportedTokenException(msg);
        }
        AuthenticationInfo info = realm.getAuthenticationInfo(token);
        if (info == null) {
            String msg = "Realm [" + realm + "] was unable to find account data for the " +
                    "submitted AuthenticationToken [" + token + "].";
            throw new UnknownAccountException(msg);
        }
        return info;
    }
```

5. *继续(Step into)进入`realm.getAuthenticationInfo(token)`,既AuthenticatingRealm.java*
```java
 public final AuthenticationInfo getAuthenticationInfo(AuthenticationToken token) throws AuthenticationException {

        AuthenticationInfo info = getCachedAuthenticationInfo(token);
        if (info == null) {
            //otherwise not cached, perform the lookup:
            info = doGetAuthenticationInfo(token);
            log.debug("Looked up AuthenticationInfo [{}] from doGetAuthenticationInfo", info);
            if (token != null && info != null) {
                cacheAuthenticationInfoIfPossible(token, info);
            }
        } else {
            log.debug("Using cached authentication info [{}] to perform credentials matching.", info);
        }

        if (info != null) {
            assertCredentialsMatch(token, info);
        } else {
            log.debug("No AuthenticationInfo found for submitted AuthenticationToken [{}].  Returning null.", token);
        }

        return info;
    }
    // ······
```

*(Step into)进入`doGetAuthenticationInfo(token)`,既SimpleAccountRealm.java. 通过`getUser(upToken.getUsername())`进行验证用户账户信息(稍后验证密码信息),然后返回一个SimpleAccount对象(account,如root),其中包含了凭证信息(credentials,如yubuntu0109)*
```java
protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken token) throws AuthenticationException {
        UsernamePasswordToken upToken = (UsernamePasswordToken) token;
        SimpleAccount account = getUser(upToken.getUsername());

        if (account != null) {

            if (account.isLocked()) {
                throw new LockedAccountException("Account [" + account + "] is locked.");
            }
            if (account.isCredentialsExpired()) {
                String msg = "The credentials for account [" + account + "] are expired";
                throw new ExpiredCredentialsException(msg);
            }

        }

        return account;
    }
    // ······
```

*`getUser(String username)`的源码如下所示 :*
```java
protected SimpleAccount getUser(String username) {
        USERS_LOCK.readLock().lock();
        try {
            return this.users.get(username);
        } finally {
            USERS_LOCK.readLock().unlock();
        }
    }
    // ······
```

*(step out)退出`doGetAuthenticationInfo(AuthenticationToken token)`方法,然后(step into)进入`assertCredentialsMatch(token, info)`方法,其属于AuthenticatingRealm.java. 继而执行验证用户密码信息的操作*
```java
protected void assertCredentialsMatch(AuthenticationToken token, AuthenticationInfo info) throws AuthenticationException {
        CredentialsMatcher cm = getCredentialsMatcher();
        if (cm != null) {
            if (!cm.doCredentialsMatch(token, info)) {
                //not successful - throw an exception to indicate this:
                String msg = "Submitted credentials for token [" + token + "] did not match the expected credentials.";
                throw new IncorrectCredentialsException(msg);
            }
        } else {
            throw new AuthenticationException("A CredentialsMatcher must be configured in order to verify " +
                    "credentials during authentication.  If you do not wish for credentials to be examined, you " +
                    "can configure an " + AllowAllCredentialsMatcher.class.getName() + " instance.");
        }
    }
```

*(step into)进入`cm.doCredentialsMatch(token, info)`,其属于SimpleCredentialsMatcher.java. 观察Shiro是如何验证密码的 : 比较AuthenticationToken(`token`)与AuthenticationInfo(`info`)中的用户密码是否相同*
```java
public boolean doCredentialsMatch(AuthenticationToken token, AuthenticationInfo info) {
        Object tokenCredentials = getCredentials(token);
        Object accountCredentials = getCredentials(info);
        return equals(tokenCredentials, accountCredentials);
    }
```
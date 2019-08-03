---
title: Shiro之自定义realm及其加密
date: 2019-08-01 18:49:46
tags: [Java,Shiro]
---

## 学习笔记 :  Shiro之自定义realm及其加密

### 自定义Realm
*`Realm` : 域,Shiro从Realm获取安全数据(如用户、角色、权限),就是说SecurityManager要验证用户身份,那么它需要从Realm获取相应的用户进行比较以确定用户身份是否合法. 也需要从Realm得到用户相应的角色 / 权限进行验证用户是否能进行操作,可以把Realm看成 DataSource,即安全数据源. 如我们之前的ini配置方式使用的是`org.apache.shiro.realm.text.IniRealm`接口*

*org.apache.shiro.realm.Realm接口如下所示 :*
```java
//返回一个唯一的Realm名字
String getName(); 
//判断此Realm是否支持此Token
boolean supports(AuthenticationToken token); 
//根据Token获取认证信息
AuthenticationInfo getAuthenticationInfo(AuthenticationToken token) throws AuthenticationException;  
```

*下面通过自定义Realm来实现一个简单的用户身份验证程序~*
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
    <!-- java.lang.NoClassDefFoundError: org/apache/commons/logging/LogFactory -->
    <dependency>
        <groupId>commons-logging</groupId>
        <artifactId>commons-logging</artifactId>
        <version>1.2</version>
    </dependency>
</dependencies>
```

2. *my-shiro.ini : 存储用户身份信息(账户=密码)*
```ini
#自定义realm
myRealm = pers.huangyuhui.realm.MyRealm
#指定SecurityManager的realms实现
securityManager.realm = $myRealm
```

3. *MyRealm.java : 自定义Realm,模拟从数据库中获取用户信息*
```java
package pers.huangyuhui.realm;

import org.apache.shiro.authc.AuthenticationException;
import org.apache.shiro.authc.AuthenticationInfo;
import org.apache.shiro.authc.AuthenticationToken;
import org.apache.shiro.authc.SimpleAuthenticationInfo;
import org.apache.shiro.authz.AuthorizationInfo;
import org.apache.shiro.realm.AuthorizingRealm;
import org.apache.shiro.subject.PrincipalCollection;

/**
 * @project: shiro-learning
 * @description: 自定义Realm
 * @author: 黄宇辉
 * @date: 8/1/2019-5:59 PM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public class MyRealm extends AuthorizingRealm {

    @Override
    public String getName() {
        return "myRealm"; //区分不同的Realm
    }

    @Override
    //授权操作
    protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principals) {
        return null;
    }

    @Override
    //认证操作:其token存储着传入的用户登录信息(usernamePasswordToken)
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken token) throws AuthenticationException {

        //获取并验证用户账号信息
        String username = (String) token.getPrincipal();
        if (!"root".equals(username)) {
            return null;
        }
        //假设用户密码信息
        String password = "yubuntu0109";

        //SimpleAuthenticationInfo(Object principal, Object credentials, String realmName)
        SimpleAuthenticationInfo info = new SimpleAuthenticationInfo(username, password, getName());
        return info;
    }
}
```

4. *LoginByMyRealm.java : 验证用户登录信息*
```java
package pers.huangyuhui.realm;

import org.apache.shiro.SecurityUtils;
import org.apache.shiro.authc.AuthenticationException;
import org.apache.shiro.authc.IncorrectCredentialsException;
import org.apache.shiro.authc.UnknownAccountException;
import org.apache.shiro.authc.UsernamePasswordToken;
import org.apache.shiro.config.IniSecurityManagerFactory;
import org.apache.shiro.mgt.SecurityManager;
import org.apache.shiro.subject.Subject;
import org.apache.shiro.util.Factory;

/**
 * Shiro认证测试:验证用户登录信息
 */
public class loginByMyRealm {

    public static void main(String[] args) {
        //1:加载配置文件,创建SecurityManager工厂对象
        Factory<SecurityManager> factory = new IniSecurityManagerFactory("classpath:my-shiro.ini");
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
            System.err.println("用户账户错误 !");
        } catch (IncorrectCredentialsException e) {
            System.err.println("用户密码错误 !");
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
// ······
用户身份是否验证成功 :true
// ······
身份身份是否注销成功 :true
```



### 加密Realm
*Shiro提供了base64和16进制字符串编码 / 解码的API支持,方便一些编码解码操作. Shiro内部的一些数据的存储及表示都使用了 base64和16进制字符串*

#### 散列算法
*散列算法一般用于生成数据的摘要信息,是一种不可逆的算法,一般适合存储密码之类的数据,常见的散列算法如`MD5`、`SHA`等. 一般进行散列时最好提供一个salt(盐),比密码"admin"加密后产生的散列值是"21232f297a57a5a743894a0e4a801fc3",其可以到一些MD5解密网站很容易的通过散列值得到密码"admin",即如果直接对密码进行散列相对来说破解更容易,此时我们可以加一些只有系统知道的干扰数据,如在密码中混入用户ID及用户名(加盐操作),既而这样生成的散列值相对来说更难破解哟!*

#### 加密自定义Realm
*接下来通过使用MD5加密自定义Realm,来完成一个简单的用户身份验证程序~*
1. *pom.xml : Maven依赖(同上)*

2. *en-shiro.ini : 存储用户身份及加密配置信息*
```ini
[main]
#定义凭证匹配器
credentialsMatcher = org.apache.shiro.authc.credential.HashedCredentialsMatcher
#散列算法
credentialsMatcher.hashAlgorithmName = md5
#散列次数
credentialsMatcher.hashIterations = 10
#将凭证匹配器设置到realm
myRealm = pers.huangyuhui.encryption.EncryRealm
myRealm.credentialsMatcher = $credentialsMatcher
securityManager.realms = $myRealm
```

3. *EncryRealm.java : 自定义Realm,模拟从数据库中获取用户信息*
```java
package pers.huangyuhui.encryption;

import org.apache.shiro.authc.AuthenticationException;
import org.apache.shiro.authc.AuthenticationInfo;
import org.apache.shiro.authc.AuthenticationToken;
import org.apache.shiro.authc.SimpleAuthenticationInfo;
import org.apache.shiro.authz.AuthorizationInfo;
import org.apache.shiro.realm.AuthorizingRealm;
import org.apache.shiro.subject.PrincipalCollection;
import org.apache.shiro.util.ByteSource;

/**
 * @project: shiro-learning
 * @description: 加密Realm
 * @author: 黄宇辉
 * @date: 8/1/2019-7:58 PM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public class EncryRealm extends AuthorizingRealm {

    @Override
    public String getName() {
        return "pwdRealm"; //区分不同的Realm
    }

    @Override
    //授权操作
    protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principals) {
        return null;
    }

    @Override
    //认证操作
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken token) throws AuthenticationException {

        //获取用户账户信息
        String username = token.getPrincipal().toString();
        if (!"root".equals(username)) {
            return null;
        }
        //MD5加密后的用户密码信息
        String password = "f3005f7acf36cec973498845460b0c33";//password + username + 10
        //指定盐值:ByteSource.Util.bytes(username)
        return new SimpleAuthenticationInfo(username, password, ByteSource.Util.bytes(username), getName());
    }
}
```

4. *LoginByMyRealm.java : 验证用户登录信息*
```java
package pers.huangyuhui.encryption;

import org.apache.shiro.SecurityUtils;
import org.apache.shiro.authc.AuthenticationException;
import org.apache.shiro.authc.IncorrectCredentialsException;
import org.apache.shiro.authc.UnknownAccountException;
import org.apache.shiro.authc.UsernamePasswordToken;
import org.apache.shiro.config.IniSecurityManagerFactory;
import org.apache.shiro.mgt.SecurityManager;
import org.apache.shiro.subject.Subject;
import org.apache.shiro.util.Factory;

/**
 * Shiro认证测试:验证用户登录信息
 */
public class loginByMyRealm {

    public static void main(String[] args) {
        //1:加载配置文件,创建SecurityManager工厂对象
        Factory<SecurityManager> factory = new IniSecurityManagerFactory("classpath:en-shiro.ini");
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
            System.err.println("用户账户错误 !");
        } catch (IncorrectCredentialsException e) {
            System.err.println("用户密码错误 !");
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
// ······
用户身份是否验证成功 :true
// ······
身份身份是否注销成功 :true
```
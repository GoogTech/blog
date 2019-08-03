---
title: Shiro之用户授权
date: 2019-08-02 08:28:58
tags: [Java,Shiro]
---

## 学习笔记 : 详解Shiro的用户授权
*简介 : 授权,也叫访问控制,即在应用中控制谁能访问哪些资源(如访问页面/编辑数据/页面操作等). 在授权中需了解的几个关键对象 : 主体(Subject)、资源(Resource)、权限(Permission)、角色(Role),其解析如下所示 :*

1. `主体` : *主体,即访问应用的用户,在Shiro中使用Subject代表该用户. 用户只有授权后才允许访问相应的资源*
2. `资源` : *在应用中用户可以访问的任何资源,比如访问JSP页面、查看/编辑某些数据、访问某个业务方法、打印文本等等..用户需要授权后方可访问*
3. `权限` : *安全策略中的原子授权单位,可用权限控制用户在应用中是否能访问某个资源,如访问用户列表页面,查看/新增/修改/删除用户数据(基本为CRUD式权限控制)..*
4. `角色` : *角色代表了操作集合,可以理解为权限的集合,一般情况下我们会赋予用户角色而不是权限,即这样用户可以拥有一组权限,不同的角色拥有一组不同的权限*

   * `隐式角色` : *即直接通过角色来验证用户有没有操作权限,即粒度是以角色为单位进行访问控制的,粒度较粗. 若进行变更可能需要多处代码的修改*
   * `显示角色` : *在程序中通过权限控制谁能访问某个资源,角色聚合一组权限集合. 这样若需要哪个角色不能访问某个资源,只需要从角色代表的权限集合中移除指定的访问权限即可,无须修改多处代码*


### 授权方式
*Shiro 支持三种方式的授权,如下所示 :*
1. *`编程式` : 通过写if/else授权代码块完成*
```java
Subject subject = SecurityUtils.getSubject();
if(subject.hasRole("admin")) {
    //有权限
} else {
    //无权限
}
```

2. *`注解式` : 通过在执行的Java方法上放置相应的注解完成*
```java
@RequiresRoles("admin")
public void hello() {
    //有权限
}
```

3. *`JSP/GSP 标签` : 在JSP/GSP页面通过相应的标签完成*
```html
<shiro:hasRole name="admin">
<!— 有权限 —>
</shiro:hasRole>
```


### 授权流程
![](Shiro之用户授权/Shiro-授权流程.png)

1. *首先调用`Subject.isPermitted*/hasRole*`接口,其会委托给SecurityManager,而SecurityManager接着会委托给Authorizer*
2. *Authorizer是真正的授权者,如果我们调用如isPermitted("user:create"),其首先会通过PermissionResolver把字符串转换成相应的Permission实例*
3. *在进行授权之前,其会调用相应的Realm获取Subject相应的角色/权限用于匹配传入的角色/权限*
4. *Authorizer会判断Realm的角色/权限是否和传入的匹配,如果有多个Realm,则会委托给ModularRealmAuthorizer进行循环判断,如果匹配如`isPermitted*/hasRole*`会返回true,否则返回false以表示授权失败*


### ini方式检查用户拥有的角色
*通过加载`ini`配置文件的方式,来实现一个用于检查用户是否拥有指定角色的程序~*

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

2. *per-shiro.ini : 用户身份及权限配置信息*
```ini
#权限表达式的定义:首先根据用户名查找角色,再根据角色查找权限,角色是权限的集合

[users]
#用户hunagyuhui的密码为loveyourself,且具有student和programmer两个角色
huangyuhui = loveyourself,student,programmer

[roles]
#角色student对资源'user'拥有create,update权限
student = user:create,user:update
#角色programmer对资源'user'具有insert,delete权限
programmer = user:read,user:delete
```

3. *RolesTest.java : 验证用户角色信息*
```java
package pers.huangyuhui.permission;

import org.apache.shiro.SecurityUtils;
import org.apache.shiro.authc.AuthenticationException;
import org.apache.shiro.authc.IncorrectCredentialsException;
import org.apache.shiro.authc.UnknownAccountException;
import org.apache.shiro.authc.UsernamePasswordToken;
import org.apache.shiro.authz.UnauthorizedException;
import org.apache.shiro.config.IniSecurityManagerFactory;
import org.apache.shiro.mgt.SecurityManager;
import org.apache.shiro.subject.Subject;
import org.apache.shiro.util.Factory;

import java.util.Arrays;
import java.util.List;


public class RolesTest {

    public static void main(String[] args) {
        //1:加载配置文件,创建SecurityManager工厂对象
        Factory<SecurityManager> factory = new IniSecurityManagerFactory("classpath:per-shiro.ini");
        //2:获得securityManager实例对象
        SecurityManager securityManager = factory.getInstance();
        //3:将securityManger实例绑定到当前运行环境中,便于访问
        SecurityUtils.setSecurityManager(securityManager);
        //4:创建当前登录的主体
        Subject currentUser = SecurityUtils.getSubject();
        //5:绑定主体登录的身份/凭证,既账户及密码
        UsernamePasswordToken token = new UsernamePasswordToken("huangyuhui", "loveyourself");
        //6:主体登录
        try {
            currentUser.login(token);
            System.out.println("用户身份是否验证成功 :" + currentUser.isAuthenticated());
            //7:进行用户角色判断
            System.out.println("判断当前登录用户是否拥有'student'角色 : " + currentUser.hasRole("student"));
            System.out.println("判断当前登录用户是否同时拥有'student'与'programmer'角色 : " + currentUser.hasAllRoles(List.of("student", "programmer")));
            System.out.println("判断当前登录用户是否拥有'student','programmer','singer'角色 : " + Arrays.toString(currentUser.hasRoles(List.of("student", "programmer", "singer"))));
            //判断当前用户是否拥有某个角色,若拥有该角色则不做任何操作(无返回值),反之则抛出:UnauthorizedException
            currentUser.checkRole("singer");

        } catch (UnknownAccountException e) {
            System.err.println("用户账户错误 !");
        } catch (IncorrectCredentialsException e) {
            System.err.println("用户密码错误 !");
        } catch (UnauthorizedException e) {
            System.err.println("用户并不拥有'singer'角色 !");
        } catch (AuthenticationException e) {
            e.printStackTrace();
        }
        //8:注销登录
        currentUser.logout();
        System.out.println("用户信息是否注销成功 :" + !currentUser.isAuthenticated());

    }
}
```

4. *程序运行结果如下所示 :*
```java
// ······
用户身份是否验证成功 :true
// ······
判断当前登录用户是否拥有'student'角色 : true
// ······
判断当前登录用户是否同时拥有'student'与'programmer'角色 : true
// ······
判断当前登录用户是否拥有'student','programmer','singer'角色 : [true, true, false]
// ······
用户并不拥有'singer'角色 !
// ······
用户信息是否注销成功 :true
```


### ini方式检查用户拥有的权限
*通过加载`ini`配置文件的方式,来实现一个用于检查用户是否拥有指定权限的程序~*

1. *pom.xml : Maven依赖(同上)*

2. *per-shiro.ini : 用户身份及权限配置信息(同上)*

3. *PermissionTest.java : 验证用户权限信息*
```java
package pers.huangyuhui.permission;

import org.apache.shiro.SecurityUtils;
import org.apache.shiro.authc.AuthenticationException;
import org.apache.shiro.authc.IncorrectCredentialsException;
import org.apache.shiro.authc.UnknownAccountException;
import org.apache.shiro.authc.UsernamePasswordToken;
import org.apache.shiro.authz.UnauthorizedException;
import org.apache.shiro.config.IniSecurityManagerFactory;
import org.apache.shiro.mgt.SecurityManager;
import org.apache.shiro.subject.Subject;
import org.apache.shiro.util.Factory;

import java.util.Arrays;


public class PermissionsTest {

    public static void main(String[] args) {
        //1:加载配置文件,创建SecurityManager工厂对象
        Factory<SecurityManager> factory = new IniSecurityManagerFactory("classpath:per-shiro.ini");
        //2:获得securityManager实例对象
        SecurityManager securityManager = factory.getInstance();
        //3:将securityManger实例绑定到当前运行环境中,便于访问
        SecurityUtils.setSecurityManager(securityManager);
        //4:创建当前登录的主体
        Subject currentUser = SecurityUtils.getSubject();
        //5:绑定主体登录的身份/凭证,既账户及密码
        UsernamePasswordToken token = new UsernamePasswordToken("huangyuhui", "loveyourself");
        //6:主体登录
        try {
            currentUser.login(token);
            System.out.println("用户身份是否验证成功 :" + currentUser.isAuthenticated());
            //7:进行用户权限判断
            System.out.println("当前用户是否拥有对资源'user'的'create'与'read'权限 : " + Arrays.toString(currentUser.isPermitted("user:create", "user:read")));
            System.out.println("当前用户是否拥有对资源'user'的'update'与'delete'权限 : " + Arrays.toString(currentUser.isPermitted("user:update", "user:delete")));
            System.out.println("当前用户是否拥有对资源'user'的'import'与'export'权限 : " + Arrays.toString(currentUser.isPermitted("user:import", "user:export")));
            //判断当前用户是否拥有某个权限,若有则不做任何操作(无返回值),反之抛出:UnauthorizedException
            currentUser.checkPermission("user:getUserList");

        } catch (UnknownAccountException e) {
            System.err.println("用户账户错误 !");
        } catch (IncorrectCredentialsException e) {
            System.err.println("用户密码错误 !");
        } catch (UnauthorizedException e) {
            System.err.println("当前用户并未拥有对资源'user'的'getUserList'权限 !");
        } catch (AuthenticationException e) {
            e.printStackTrace();
        }
        //8:注销登录
        currentUser.logout();
        System.out.println("用户信息是否注销成功 :" + !currentUser.isAuthenticated());

    }
}
```

4. *程序运行结果如下所示 :*
```java
// ······
用户身份是否验证成功 :true
// ······
当前用户是否拥有对资源'user'的'create'与'read'权限 : [true, true]
// ······
当前用户是否拥有对资源'user'的'update'与'delete'权限 : [true, true]
// ······
当前用户是否拥有对资源'user'的'import'与'export'权限 : [false, false]
// ······
当前用户并未拥有对资源'user'的'getUserList'权限 !
// ······
用户信息是否注销成功 :true
```


### 自定义Realm检查用户拥有的权限
*通过配置自定义`Realm`的方式,来实现一个用于验证用于角色及权限的程序~*
1. *pom.xml : Maven依赖(同上)*

2. *per-my-shiro.ini : 配置自定义Realm*
```ini
#指定自定义realm
myRealm = pers.huangyuhui.permission.realm.PermissRealm
#指定SecurityManager的realms实现
securityManager.realm = $myRealm
```

3. *PermissionRealm.java : 自定义Realm,并配置用户角色及权限信息*
```java
package pers.huangyuhui.permission.realm;

import org.apache.shiro.authc.AuthenticationException;
import org.apache.shiro.authc.AuthenticationInfo;
import org.apache.shiro.authc.AuthenticationToken;
import org.apache.shiro.authc.SimpleAuthenticationInfo;
import org.apache.shiro.authz.AuthorizationInfo;
import org.apache.shiro.authz.SimpleAuthorizationInfo;
import org.apache.shiro.realm.AuthorizingRealm;
import org.apache.shiro.subject.PrincipalCollection;

import java.util.ArrayList;
import java.util.List;

/**
 * @project: shiro-learning
 * @description: 自定义Realm, 配置用户角色及权限信息
 * @author: 黄宇辉
 * @date: 8/2/2019-10:52 AM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public class PermissRealm extends AuthorizingRealm {

    @Override
    public String getName() {
        return "permissRealm";
    }

    @Override
    //授权:其principals存储着用户认证的凭证信息
    protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principals) {

        //获取当前登录的用户名信息,既用户凭证
        //Object username = principals.getPrimaryPrincipal();

        //模拟查询数据库操作:查询用户所拥有的的角色及权限信息
        List<String> roles = new ArrayList<>();
        List<String> permiss = new ArrayList<>();
        //假设该用户拥有'student'与'programmer'角色
        roles.add("student");
        roles.add("programmer");
        //假设该用户拥有对资源'user'的'read'与'update'权限
        permiss.add("user:create");
        permiss.add("user:update");

        //封装用户的角色与权限信息并返回
        SimpleAuthorizationInfo info = new SimpleAuthorizationInfo();
        info.addRoles(roles);
        info.addStringPermissions(permiss);
        return info;
    }

    @Override
    //认证:其token存储着传入的用户身份信息(usernamePasswordToken)
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken token) throws AuthenticationException {
        //获取用户的账户信息并验证
        String username = (String) token.getPrincipal();
        if (!"github".equals(username)) {
            return null;
        }
        //模拟用户密码信息
        String password = "yubuntu0109";

        //SimpleAuthenticationInfo(Object principal, Object credentials, String realmName)
        return new SimpleAuthenticationInfo(username, password, getName());
    }
}
```

4. *PermissTest.java : 验证用户角色及权限信息*
```java
package pers.huangyuhui.permission.realm;

import org.apache.shiro.SecurityUtils;
import org.apache.shiro.authc.AuthenticationException;
import org.apache.shiro.authc.IncorrectCredentialsException;
import org.apache.shiro.authc.UnknownAccountException;
import org.apache.shiro.authc.UsernamePasswordToken;
import org.apache.shiro.authz.UnauthorizedException;
import org.apache.shiro.config.IniSecurityManagerFactory;
import org.apache.shiro.mgt.SecurityManager;
import org.apache.shiro.subject.Subject;
import org.apache.shiro.util.Factory;

import java.util.Arrays;
import java.util.List;


public class PermissTest {

    public static void main(String[] args) {
        //1:加载配置文件,创建SecurityManager工厂对象
        Factory<SecurityManager> factory = new IniSecurityManagerFactory("classpath:my-per-shiro.ini");
        //2:获得securityManager实例对象
        SecurityManager securityManager = factory.getInstance();
        //3:将securityManger实例绑定到当前运行环境中,便于访问
        SecurityUtils.setSecurityManager(securityManager);
        //4:创建当前登录的主体
        Subject currentUser = SecurityUtils.getSubject();
        //5:绑定主体登录的身份/凭证,既账户及密码
        UsernamePasswordToken token = new UsernamePasswordToken("github", "yubuntu0109");
        //6:主体登录
        try {
            currentUser.login(token);
            System.out.println("用户身份是否验证成功 :" + currentUser.isAuthenticated());
            //7:验证用户角色
            System.out.println("判断当前登录用户是否拥有'student','programmer','singer'角色 : "
                    + Arrays.toString(currentUser.hasRoles(List.of("student", "programmer", "singer"))));
            //8:验证用户权限
            System.out.println("当前用户是否拥有对'user'资源的'create','update','read','delete'权限 : "
                    + Arrays.toString(currentUser.isPermitted("user:create", "user:update", "user:read", "user:delete")));
        } catch (UnknownAccountException e) {
            System.err.println("用户账户错误 !");
        } catch (IncorrectCredentialsException e) {
            System.err.println("用户密码错误 !");
        } catch (AuthenticationException e) {
            e.printStackTrace();
        }
        //8:注销登录
        currentUser.logout();
        System.out.println("用户信息是否注销成功 :" + !currentUser.isAuthenticated());

    }
}
```

5. *程序运行结果如下所示 :*
```java
// ······
用户身份是否验证成功 :true
// ······
判断当前登录用户是否拥有'student','programmer','singer'角色 : [true, true, false]
// ······
当前用户是否拥有对'user'资源的'create','update','read','delete'权限 : [true, true, false, false]
// ······
用户信息是否注销成功 :true
```
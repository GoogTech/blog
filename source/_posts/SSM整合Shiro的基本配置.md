---
title: SSM整合Shiro的基本配置
date: 2019-08-11 06:55:55
tags: [SSM,Shiro]
---

## 学习笔记 : SSM整合Shiro的基本配置
*学习Spring Boot整合Shiro知识后,写了[一个简单的Spring Boot整合Shiro的权限管理案例](https://github.com/YUbuntu0109/Shiro-learning/tree/master/spring%20boot%20project%20with%20shiro),可随后在GitHub上发现了一个[基于SSM框架简单的后台权限管理系统](https://github.com/YUbuntu0109/Permission),很喜欢该项目(比较初级,适合练手),所以又开始学习如何在SSM中整合Shiro,Spring集成Shiro一般通过的 xml 配置,相比Spring Boot较为繁琐哟~ 以下基本配置信息摘自案例 : [一个简单的SSM整合Shiro的权限管理案例](https://github.com/YUbuntu0109/Shiro-learning/tree/master/ssm%20project%20with%20shiro)*


### Maven依赖
*Shiro-learning/ssm project with shiro/ssm-shiro/pom.xml*
```xml
<!-- Shiro -->
<dependency>
    <groupId>org.apache.shiro</groupId>
    <artifactId>shiro-spring</artifactId>
    <version>1.4.1</version>
</dependency>
<!-- Ehcache -->
<dependency>
    <groupId>org.apache.shiro</groupId>
    <artifactId>shiro-ehcache</artifactId>
    <version>1.4.1</version>
</dependency>
```


### web.xml配置
*Shiro-learning/ssm project with shiro/ssm-shiro/src/main/webapp/WEB-INF/web.xml*
```xml
<!--
配置Spring整合Shiro的过滤器:将拦截下来的请求交给Spring容器中名为'shiroFilter'(与filter-name同名)的bean处理
详情信息见:applicationContext-shiro.xml
-->
<filter>
    <filter-name>shiroFilter</filter-name>
    <filter-class>org.springframework.web.filter.DelegatingFilterProxy</filter-class>
</filter>
<filter-mapping>
    <filter-name>shiroFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```


### Shiro的基本配置
*Shiro-learning/ssm project with shiro/ssm-shiro/src/main/resources/spring-config/applicationContext-shiro.xml*
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd">

    <description>configuration informations about Shiro</description>

    <!-- 配置shiroFilter:这里的id名(shiroFilter)要和web.xml配置的名字保持一致哟 -->
    <bean id="shiroFilter" class="org.apache.shiro.spring.web.ShiroFilterFactoryBean">
        <!-- 关联SecurityManage -->
        <property name="securityManager" ref="securityManager"/>
        <!-- 定义过虑器链,从上向下顺序执行,一般将/**放在最下边 -->
        <property name="filterChainDefinitions">
            <value>
                <!-- 防止过滤器拦截验证用户登录信息的请求(/login ) -->
                /login=anon
                <!-- 设置Shiro默认的注销请求:其具体的注销逻辑代码Shiro已经替我们实现了哟 -->
                /logout=logout
                <!-- 设置角色过滤 -->
                /teaListView=roles[admin]
                <!-- 设置请求权限过滤:除身份认证外,还需要用户拥有对stuListView/teaListView资源的view权限 -->
                /stuListView=perms[stuListView:view]
                /teaListView=perms[teaListView:view]
                <!-- 所有资源的访问都需要身份认证 -->
                /**=authc
            </value>
        </property>
        <!-- 指定Shiro默认的登录请求,默认: http://localhost:8080/login.jsp -->
        <property name="loginUrl" value="/loginView"/>
        <!-- 指定用户未授权操作提示页 -->
        <property name="unauthorizedUrl" value="/unauthorizedView"/>
    </bean>

    <!-- 自定义realm -->
    <bean id="realm" class="pers.huangyuhui.shiro.shiro.UserRealm">
        <!-- 用户凭证加密配置 -->
        <property name="credentialsMatcher" ref="credentialsMatcher"/>
        <!-- 是否启用缓存:默认为true -->
        <property name="cachingEnabled" value="true"/>
        <!-- 是否启用身份验证缓存 -->
        <property name="authenticationCachingEnabled" value="true"/>
        <!-- 缓存AuthenticationInfo信息的缓存名称 -->
        <property name="authenticationCacheName" value="authenticationCache"/>
        <!-- 是否启用授权缓存:缓存AuthorizationInfo信息 -->
        <property name="authorizationCachingEnabled" value="true"/>
        <!-- 缓存AuthorizationInfo信息的缓存名称 -->
        <property name="authorizationCacheName" value="authorizationCache"/>
    </bean>

    <!-- Shiro安全管理器 -->
    <bean id="securityManager" class="org.apache.shiro.web.mgt.DefaultWebSecurityManager">
        <!-- 注入自定义Realm -->
        <property name="realm" ref="realm"/>
        <!-- 注入缓存管理器 -->
        <property name="cacheManager" ref="ehCacheManager"/>
    </bean>

    <!-- Shiro凭证匹配器:对登录用户的密码进行MD5加密,hashIterations是散列加密次数,盐值信息见:UserRealm.java -->
    <bean id="credentialsMatcher" class="org.apache.shiro.authc.credential.HashedCredentialsMatcher">
        <property name="hashAlgorithmName" value="MD5"/>
        <property name="hashIterations" value="3"/>
    </bean>

    <!-- Shiro缓存管理器 -->
    <bean id="ehCacheManager" class="org.apache.shiro.cache.ehcache.EhCacheManager">
        <!-- 注入ehcache配置文件 -->
        <property name="cacheManagerConfigFile" value="classpath:shiro-config/ehcache-shiro.xml"/>
    </bean>

    <!-- Shiro生命周期处理器 -->
    <bean id="lifecycleBeanPostProcessor" class="org.apache.shiro.spring.LifecycleBeanPostProcessor"/>

    <!-- 开启shiro注解模式:如果使用注解模式,必须采用cglib针对类进行代理 -->
    <bean class="org.springframework.aop.framework.autoproxy.DefaultAdvisorAutoProxyCreator"
          depends-on="lifecycleBeanPostProcessor">
        <property name="proxyTargetClass" value="true"/>
    </bean>
    <bean class="org.apache.shiro.spring.security.interceptor.AuthorizationAttributeSourceAdvisor">
        <property name="securityManager" ref="securityManager"/>
    </bean>

</beans>
```

*Shiro-learning/ssm project with shiro/ssm-shiro/src/main/resources/shiro-config/ehcache-shiro.xml*
```xml
<?xml version="1.0" encoding="UTF-8"?>
<ehcache xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:noNamespaceSchemaLocation="http://ehcache.org/ehcache.xsd"
         updateCheck="false"
         name="myEhcache">
    <diskStore path="java.io.tmpdir"/>
    <!-- 授权信息缓存 -->
    <cache name="authorizationCache"
           maxEntriesLocalHeap="2000"
           timeToIdleSeconds="1800"
           timeToLiveSeconds="1800"
           overflowToDisk="false"
           statistics="true">
    </cache>
    <!-- 身份信息缓存 -->
    <cache name="authenticationCache"
           maxEntriesLocalHeap="2000"
           timeToIdleSeconds="1800"
           timeToLiveSeconds="1800"
           overflowToDisk="false"
           statistics="true">
    </cache>
    <!-- Session缓存 -->
    <cache name="activeSessionCache"
           maxEntriesLocalHeap="2000"
           timeToIdleSeconds="1800"
           timeToLiveSeconds="1800"
           overflowToDisk="false"
           statistics="true">
    </cache>
    <!-- 缓存半小时 -->
    <cache name="halfHour"
           maxElementsInMemory="10000"
           maxElementsOnDisk="100000"
           timeToIdleSeconds="1800"
           timeToLiveSeconds="1800"
           overflowToDisk="false"
           diskPersistent="false"/>
    <!-- 缓存一小时 -->
    <cache name="hour"
           maxElementsInMemory="10000"
           maxElementsOnDisk="100000"
           timeToIdleSeconds="3600"
           timeToLiveSeconds="3600"
           overflowToDisk="false"
           diskPersistent="false"/>
    <!-- 缓存一天 -->
    <cache name="oneDay"
           maxElementsInMemory="10000"
           maxElementsOnDisk="100000"
           timeToIdleSeconds="86400"
           timeToLiveSeconds="86400"
           overflowToDisk="false"
           diskPersistent="false"/>

    <!--
         name: 缓存名称
         maxElementsInMemory: 缓存最大个数
         eternal: 对象是否永久有效,一但设置了,timeout将不起作用
         timeToIdleSeconds: 设置对象在失效前的允许闲置时间(单位：秒). 仅当eternal=false对象不是永久有效时使用,可选属性,默认值是0,也就是可闲置时间无穷大
         timeToLiveSeconds: 设置对象在失效前允许存活时间(单位：秒). 最大时间介于创建时间和失效时间之间. 仅当eternal=false对象不是永久有效时使用,默认是0,也就是对象存活时间无穷大
         overflowToDisk: 当内存中对象数量达到maxElementsInMemory时,Ehcache将会对象写到磁盘中
         diskSpoolBufferSizeMB: 这个参数设置DiskStore(磁盘缓存)的缓存区大小,默认是30MB. 每个Cache都应该有自己的一个缓冲区
         maxElementsOnDisk: 硬盘最大缓存个数
         diskPersistent: 是否缓存虚拟机重启期数据 Whether the disk store persists between restarts of the Virtual Machine. The default value is false.
         diskExpiryThreadIntervalSeconds: 磁盘失效线程运行时间间隔,默认是120秒
         memoryStoreEvictionPolicy: 当达到maxElementsInMemory限制时,Ehcache将会根据指定的策略去清理内存. 默认策略是LRU(最近最少使用). 你可以设置为FIFO(先进先出)或是LFU(较少使用)
         clearOnFlush: 内存数量最大时是否清除
     -->
    <defaultCache name="defaultCache"
                  maxElementsInMemory="10000"
                  timeToIdleSeconds="600"
                  timeToLiveSeconds="600"
                  overflowToDisk="false"
                  maxElementsOnDisk="100000"
                  diskPersistent="false"
                  diskExpiryThreadIntervalSeconds="120"
                  memoryStoreEvictionPolicy="LRU">
    </defaultCache>
</ehcache>
```


### 自定义Realm的基本配置
*Shiro-learning/ssm project with shiro/ssm-shiro/src/main/java/pers/huangyuhui/shiro/shiro/UserRealm.java*
```java
package pers.huangyuhui.shiro.shiro;

import org.apache.shiro.SecurityUtils;
import org.apache.shiro.authc.*;
import org.apache.shiro.authz.AuthorizationInfo;
import org.apache.shiro.authz.SimpleAuthorizationInfo;
import org.apache.shiro.realm.AuthorizingRealm;
import org.apache.shiro.subject.PrincipalCollection;
import org.apache.shiro.subject.Subject;
import org.apache.shiro.util.ByteSource;
import org.springframework.beans.factory.annotation.Autowired;
import pers.huangyuhui.shiro.bean.Permission;
import pers.huangyuhui.shiro.bean.Role;
import pers.huangyuhui.shiro.bean.User;
import pers.huangyuhui.shiro.service.UserService;

import java.util.Collection;
import java.util.HashSet;
import java.util.Set;

/**
 * @project: ssm-shiro
 * @description: 配置自定义Realm
 * @author: 黄宇辉
 * @date: 8/10/2019-1:11 PM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public class UserRealm extends AuthorizingRealm {

    @Autowired
    private UserService userService;

    /**
     * @description: 授权认证:提供用户信息,返回权限信息
     * @param: principals
     * @date: 2019-08-11 7:50 AM
     * @return: org.apache.shiro.authz.AuthorizationInfo
     */
    @Override
    protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principals) {
        System.err.println("------------> 授权认证 ------------>");
        //获取当前登录的用户信息
        Subject currentUser = SecurityUtils.getSubject();
        User u = (User) currentUser.getPrincipal();
        //从数据库中获取用户所拥有的角色及权限信息
        User user = userService.findByName(u.getUsername());
        if (user != null) {
            SimpleAuthorizationInfo info = new SimpleAuthorizationInfo();
            //用于存储用户的角色及权限信息
            Collection<String> rolesCollection = new HashSet<>();
            Collection<String> permissionsCollection = new HashSet<>();
            Set<Role> roles = user.getRoles(); //获取用户Role的Set集合
            //通过遍历用户所拥有的角色,来获取其对应的权限信息
            for (Role role : roles) {
                rolesCollection.add(role.getName()); //将每一个role的name封装到集合中
                Set<Permission> permissionSet = role.getPermissions(); //获取每一个role所对应的permission的set集合
                //遍历用户所拥有的权限信息
                for (Permission permission : permissionSet) {
                    permissionsCollection.add(permission.getName()); //将每一个permission的name封装到集合中
                }
                info.addStringPermissions(permissionsCollection); //为用户授权
            }
            info.addRoles(rolesCollection); //为用户授予角色
            System.out.println("[roles]------------>" + rolesCollection.toString());
            System.out.println("[permissions]------------>" + permissionsCollection.toString());
            return info;
        }
        return null;

    }

    /**
     * @description: 登录认证:提供帐户信息,返回认证信息
     * @param: authenticationToken
     * @date: 2019-08-11 7:51 AM
     * @return: org.apache.shiro.authc.AuthenticationInfo
     */
    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken authenticationToken) throws AuthenticationException {
        System.err.println("------------> 开始认证 ------------>");
        UsernamePasswordToken token = (UsernamePasswordToken) authenticationToken;
        //从数据库中获取用户信息
        User user = userService.findByName(token.getUsername());
        System.out.println("[user]------------>" + user);
        //验证账户信息
        if (user == null) {
            return null; //it's will be throw a UnknownAccountException
        }
        //验证密码信息(盐值:ByteSource.Util.bytes(user.getUsername()))
        return new SimpleAuthenticationInfo(user, user.getPassword(), ByteSource.Util.bytes(user.getUsername()), this.getName());
    }
}
```
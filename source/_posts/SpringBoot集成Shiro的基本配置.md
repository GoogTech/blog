---
title: SpringBoot集成Shiro的基本配置
date: 2019-08-06 15:13:51
tags: [SpringBoot,Shiro]
---

## 学习笔记 : SpringBoot集成Shiro的基本配置
*Spring集成Shiro一般通过的 xml 配置,比较繁琐,而Spring Boot集成Shiro相对简单,只需要配置两个类 : ShiroConfiguration类及继承AuthorizingRealm的Realm类,如下所示 :*

1. *`ShiroConfig` : 顾名思义就是对Shiro的一些配置,相对于Spring中的xml配置. 包括 : 包括过滤器(ShiroFilter)、安全事务管理器(SecurityManager)、密码凭证匹配器(CredentialsMatcher)、缓冲管理器(EhCacheManager)、aop注解支持(authorizationAttributeSourceAdvisor)、等等*
2. *`CustomRealm` : 自定义的CustomRealm继承自AuthorizingRealm,重写了父类中的doGetAuthorizationInfo(授权认证)、doGetAuthenticationInfo(登陆认证)这两个方法*


### 基本的配置
*以下示例代码摘自 : [一个简单的SpringBoot集成Shiro的权限管理案例](https://github.com/YUbuntu0109/Shiro-learning/tree/master/spring%20boot%20project%20with%20shiro)*

#### Shiro的基本配置信息
```java
package pers.huangyuhui.ss.shiro;

import at.pollux.thymeleaf.shiro.dialect.ShiroDialect;
import net.sf.ehcache.CacheManager;
import org.apache.shiro.authc.credential.HashedCredentialsMatcher;
import org.apache.shiro.cache.ehcache.EhCacheManager;
import org.apache.shiro.mgt.SecurityManager;
import org.apache.shiro.spring.web.ShiroFilterFactoryBean;
import org.apache.shiro.web.mgt.DefaultWebSecurityManager;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import java.util.LinkedHashMap;
import java.util.Map;

/**
 * @project: springboot-shiro
 * @description: Shiro配置信息
 * @author: 黄宇辉
 * @date: 8/5/2019-7:41 AM
 * @version: 1.1
 * @website: https://yubuntu0109.github.io/
 */
@Configuration
public class ShiroConfig {

    /**
     * @description: 配置过滤器
     * @param: securityManager
     * @date: 2019-08-05 7:59 AM
     * @return: org.apache.shiro.spring.web.ShiroFilterFactoryBean
     */
    @Bean
    public ShiroFilterFactoryBean shiroFilterFactoryBean(SecurityManager securityManager) {
        ShiroFilterFactoryBean shiroFilterFactoryBean = new ShiroFilterFactoryBean();
        //设置安全管理器
        shiroFilterFactoryBean.setSecurityManager(securityManager);
        //设置自定义过滤器
        shiroFilterFactoryBean.setFilterChainDefinitionMap(filterChainDefinitionMap());
        //设置用户登录页,默认: http://localhost:8080/login.jsp
        shiroFilterFactoryBean.setLoginUrl("/loginView");
        //设置用户未授权操作提示页
        shiroFilterFactoryBean.setUnauthorizedUrl("/unauthorizedView");
        return shiroFilterFactoryBean;
    }

    /**
     * @description: 初始化自定义Realm
     * @param: credentialsMatcher
     * @date: 2019-08-05 7:50 AM
     * @return: pers.huangyuhui.ss.shiro.UserRealm
     */
    @Bean
    public UserRealm userRealm(HashedCredentialsMatcher hashedCredentialsMatcher) {
        UserRealm userRealm = new UserRealm();
        //设置凭证匹配器
        userRealm.setCredentialsMatcher(hashedCredentialsMatcher);
        return userRealm;
    }

    /**
     * @description: 安全事务管理器
     * @param: credentialsMatcher
     * @date: 2019-08-05 7:53 AM
     * @return: org.apache.shiro.web.mgt.DefaultWebSecurityManager
     */
    @Bean
    public SecurityManager securityManager(UserRealm userRealm, EhCacheManager ehCacheManager) {
        DefaultWebSecurityManager defaultWebSecurityManager = new DefaultWebSecurityManager();
        //关联自定义realm
        defaultWebSecurityManager.setRealm(userRealm);
        //关联缓存管理
        defaultWebSecurityManager.setCacheManager(ehCacheManager);
        return defaultWebSecurityManager;
    }

    /**
     * @description: 哈希密码匹配器:比较用户登录时输入的密码,跟数据库密码配合盐值salt解密后是否一致
     * @date: 2019-08-05 9:01 PM
     * @return: org.apache.shiro.authc.credential.HashedCredentialsMatcher
     */
    @Bean
    public HashedCredentialsMatcher hashedCredentialsMatcher() {
        HashedCredentialsMatcher hashedCredentialsMatcher = new HashedCredentialsMatcher();
        hashedCredentialsMatcher.setHashAlgorithmName("md5"); //散列算法
        hashedCredentialsMatcher.setHashIterations(3); //散列的次数
        hashedCredentialsMatcher.setStoredCredentialsHexEncoded(true); //默认是true:Hex编码.false:Base64编码
        return hashedCredentialsMatcher;
    }

    /**
     * @description: 设置缓存管理, 缓存用户及其权限信息
     * @date: 2019-08-07 7:51 AM
     * @return: org.apache.shiro.cache.ehcache.EhCacheManager
     */
    @Bean
    public EhCacheManager ehCacheManager() {
        //注意:myEhcache对应ehcache-shiro.xml中的'<ehcache name="myEhcache">'
        CacheManager cacheManager = CacheManager.getCacheManager("myEhcache");
        if (cacheManager == null) {
            cacheManager = CacheManager.create();
        }
        EhCacheManager ehCacheManager = new EhCacheManager();
        ehCacheManager.setCacheManager(cacheManager);
        return ehCacheManager;
    }


    /**
     * @description: 设置资源的权限控制
     * @date: 2019-08-05 8:31 AM
     * @return: java.util.Map
     */
    private Map<String, String> filterChainDefinitionMap() {
        Map<String, String> filterMap = new LinkedHashMap<>();
        //需身份认证
        filterMap.put("/stuListView", "authc");
        filterMap.put("/teaListView", "authc");
        //无需身份认证:防止验证用户登录信息操作被'filterMap.put("/**", "authc")'拦截
        filterMap.put("/login", "anon");
        //注销过滤器:其具体的注销逻辑代码Shiro已经替我们实现了哟
        filterMap.put("/logout", "logout");
        //角色过滤:需要用户拥有'admin'角色
        filterMap.put("/teaListView", "roles[admin]");
        //权限过滤:除身份认证外,还需要用户拥有对stuListView资源的view权限
        filterMap.put("/stuListView", "perms[stuListView:view]");
        filterMap.put("/teaListView", "perms[teaListView:view]");
        //拦截需要登录(用户认证)方可访问的资源(一般将/**放在最下边,不然会导致所有url都被拦截哟)
        filterMap.put("/**", "authc");
        return filterMap;
    }


    /**
     * @description: 配置ShiroDialect, 用于thymeleaf和shiro标签配合使用
     * @date: 2019-08-05 6:37 PM
     * @return: at.pollux.thymeleaf.shiro.dialect.ShiroDialect
     */
    @Bean
    public ShiroDialect shiroDialect() {
        return new ShiroDialect();
    }

}
```

*ehcache-shiro.xml : EhCache缓存框架的配置文件*
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
         name: 缓存名称。
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


#### 自定义Realm
```java
package pers.huangyuhui.ss.shiro;

import org.apache.shiro.SecurityUtils;
import org.apache.shiro.authc.*;
import org.apache.shiro.authz.AuthorizationInfo;
import org.apache.shiro.authz.SimpleAuthorizationInfo;
import org.apache.shiro.realm.AuthorizingRealm;
import org.apache.shiro.subject.PrincipalCollection;
import org.apache.shiro.subject.Subject;
import org.apache.shiro.util.ByteSource;
import org.springframework.beans.factory.annotation.Autowired;
import pers.huangyuhui.ss.bean.Permission;
import pers.huangyuhui.ss.bean.Role;
import pers.huangyuhui.ss.bean.User;
import pers.huangyuhui.ss.service.UserService;

import java.util.Collection;
import java.util.HashSet;
import java.util.Set;

/**
 * @project: springboot-shiro
 * @description: 配置自定义Realm
 * @author: 黄宇辉
 * @date: 8/5/2019-7:45 AM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public class UserRealm extends AuthorizingRealm {

    @Autowired
    private UserService userService;

    /**
     * @description: 授权认证:提供用户信息,返回权限信息
     * @param: principalCollection
     * @date: 2019-08-06 6:02 PM
     * @return: org.apache.shiro.authz.AuthorizationInfo
     */
    @Override
    protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principalCollection) {
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
     * @date: 2019-08-06 6:12 PM
     * @return: org.apache.shiro.authc.AuthenticationInfo
     */
    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken authenticationToken) throws AuthenticationException {
        System.err.println("------------> 开始认证 ------------>");
        UsernamePasswordToken token = (UsernamePasswordToken) authenticationToken;
        //从数据库中获取用户信息
        User user = userService.findByName(token.getUsername());
        //验证账户信息
        if (user == null) {
            return null; //it's will be throw a UnknownAccountException
        }
        //验证密码信息
        return new SimpleAuthenticationInfo(user, user.getPassword(), ByteSource.Util.bytes(user.getUsername()), this.getName());
    }
}
```
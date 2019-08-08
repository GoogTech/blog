---
title: SpringBoot中Shiro集成EhCache
date: 2019-08-07 12:10:32
tags: [SpringBoot,Shiro,EhCache]
---

## 学习笔记 : SpringBoot项目中Shiro集成EhCache
*示例程序摘自 : [一个简单的SpringBoot整合Shiro的权限管理案例](https://github.com/YUbuntu0109/Shiro-learning/tree/master/spring%20boot%20project%20with%20shiro)*

### 添加依赖
*Shiro-learning/spring boot project with shiro/springboot-shiro/pom.xml*
```xml
<dependencies>
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
</dependencies>
```

### 创建EhCache缓存配置文件
*Shiro-learning/spring boot project with shiro/springboot-shiro/src/main/resources/ehcache-shiro.xml*
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

### 配置Shiro缓存
*Shiro-learning/spring boot project with shiro/springboot-shiro/src/main/java/pers/huangyuhui/ss/shiro/ShiroConfig.java*
```java
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
     * @description: 设置缓存管理器, 缓存用户及其权限信息
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

    // ······
}
```

#### 注意事项
*如果使用的EhCache版本超过了2.5.0,那么`<ehcache name="myEhcache">`的配置就显得非常重要了,不然EhCache会自动加载默认的名字 : `_default_`,且EhCache2.5以后只允许创建单例的CacheManager(缓存管理器),所以应注意避免重复加载CacheManager哟 : 创建ehCacheManager的时先判断是否已存在cacheManager,没有的情况下再进行创建,判断的关键为`ehcache-shiro.xml`(缓存配置文件)中配置的`name`属性,可通过该属性来判断cacheManager是否加载了,示例代码如下 :*

```java
CacheManager cacheManager = CacheManager.getCacheManager("myEhcache");
    if (cacheManager == null) {
        cacheManager = CacheManager.create();
    }
```



*借鉴(非常感谢) : https://blog.csdn.net/Maslii/article/details/82380568*
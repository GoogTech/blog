---
title: Maven依赖经常导入失败的解决方案
date: 2019-10-21 23:25:45
tags: [Maven]
---

## 学习笔记 : Maven依赖经常导入失败的解决方案
*我最近一周在学习并贡献的项目 : [VBlog](https://github.com/YUbuntu0109/VBlog/tree/dev)( 一个基于 Vue + SpringBoot 并采用当下流行的前后端分离开发模式实现的多用户博客管理平台 ). 刚开始将该项目导入到 IDE 并尝试本地运行时,发现`Maven`依赖竟然又无法正常导入,本来满怀期待的想要在本地体验一下该项目的,哎~  为了再次避免这种尴尬,给出以下两个解决方案,基本上这两个方案配合使用真的可以解决 99% 的问题哟嘿嘿嘿~*


### 方案一
*配置 Maven 镜像站 : 以下是我一直在用的常用镜像站*
```xml
<!-- ailiyun' Maven respository mirror -->
<mirror>
    <id>aliyun</id>
    <mirrorOf>central</mirrorOf>
    <name>Nexus aliyun</name>
    <url>http://maven.aliyun.com/nexus/content/groups/public</url>
</mirror>

<mirror>
    <id>repo1</id>
    <name>repo1 maven</name>
    <url>http://repo1.maven.org/maven2</url>
    <mirrorOf>central</mirrorOf>
</mirror>

<mirror>
    <id>repo2</id>
    <name>repo2 maven</name>
    <url>http://repo2.maven.org/maven2</url>
    <mirrorOf>central</mirrorOf>
</mirror>

<mirror>
    <id>ui</id>
    <name>Mirror from UK</name>
    <url>http://uk.maven.org/maven2/</url>
    <mirrorOf>central</mirrorOf>
</mirror>

<mirror>
    <id>jboss-public-repository-group</id>
    <mirrorOf>central</mirrorOf>
    <name>JBoss Public Repository Group</name>
    <url>http://repository.jboss.org/nexus/content/groups/public</url>
</mirror>

<!--http://repo.spring.io/release/-->
<mirror>
    <id>spring-maven2</id>
    <name>spring maven2</name>
    <url>http://repo.spring.io/release/</url>
    <mirrorOf>central</mirrorOf>
</mirror>

<!--http://repo.spring.io/libs-milestone/-->
<mirror>
    <id>spring-maven1</id>
    <name>spring maven1</name>
    <url>http://repo.spring.io/libs-milestone/</url>
    <mirrorOf>central</mirrorOf>
</mirror>
```


### 方案二
*方案一配置后,发现依赖仍然下载失败 !? 这时候就需要配合使用方案二咯 : 虽然依赖下载失败了,但是你会发现相关文件夹里却多出了很多以`.lastUpdated`为后缀的文件,这也就是开发者在开发工具中反复导入依赖但始终失败的原因啦~ 这时就需要你通过文件搜索找到本地仓库( 例如 `C:\Users\Administrator\.m2\repository` )中所有以`.lastUpdated`为后缀的文件,并将其全部删除即可 !*



> 🙏 参考博客 : [两步解决maven依赖导入失败问题](https://blog.csdn.net/u012702547/article/details/88071279)
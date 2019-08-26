---
title: 'Spring Boot项目:好友备忘录'
date: 2019-06-30 20:53:09
tags: [Thymeleaf,jQuery,Ajax,EasyUI,Spring Boot,MyBatis,,MySQL,Maven]
---

## A simple project for Spring Boot ~
- *我的第一个`Spring Boot`小项目 （づ￣3￣）づ╭❤～*
- *赶紧来学习吧 ! 期待你的`issues`哟(っ•̀ω•́)っ✎⁾⁾~ 该项目的GitHub仓库地址 : https://github.com/YUbuntu0109/springboot-beginner*


### 项目概述
👍*一个简单的,基于Spring Boot的好友备忘录小项目,通过本项目可以学习`Spring Boot`与`MyBatis`的整合及CURD操作的基本思路,同时也可以帮助你学习`Thylemeaf`模板引擎使用哟 ! 该项目的代码注释详细,逻辑结构清晰,非常具有参考,学习价值哟 ! 可以说非常适合初学Sping Boot的同学啦(っ•̀ω•́)っ✎⁾⁾~*

💗*为了让更多同学快速地体验该项目,已通过`mvn package`将其打包,并将其作为`v1.0`上传到了`release`中~*


### 项目截图
- *项目主页面*

![ ](Spring-Boot项目-好友备忘录/SpringBoot-CURD-Memo_MainView.PNG)

- *好友信息管理页面*

![ ](Spring-Boot项目-好友备忘录/SpringBoot-CURD-Memo_FriendListView.PNG)

![ ](Spring-Boot项目-好友备忘录/SpringBoot-CURD-Memo_FriendListView2.PNG)


### 项目结构
```
│  .gitattributes
│  LICENSE
│  README.md
│
├─database file
│      memo.sql
│
├─demonstration_picture
│      SpringBoot-CURD-Memo_FriendListView.PNG
│      SpringBoot-CURD-Memo_FriendListView2.PNG
│      SpringBoot-CURD-Memo_MainView.PNG
│
└─memo
    │  .gitignore
    │  mvnw
    │  mvnw.cmd
    │  pom.xml
    │
    │
    └─src
        └─main
            ├─java
            │  └─pers
            │      └─haungyuhui
            │          └─memo
            │              │  MemoApplication.java
            │              │
            │              ├─bean
            │              │      Friend.java
            │              │
            │              ├─controller
            │              │      StudentController.java
            │              │
            │              ├─dao
            │              │      FriendMapper.java
            │              │      FriendMapper.xml
            │              │
            │              ├─service
            │              │  │  FriendService.java
            │              │  │
            │              │  └─impl
            │              │          FriendServiceImpl.java
            │              │
            │              └─util
            │                      UploadFile.java
            │
            └─resources
                │  application.properties
                │
                ├─static
                │  ├─easyui
                │  │  │  jquery.easyui.min.js
                │  │  │  jquery.min.js
                │  │  │
                │  │  ├─css
                │  │  │      default.css
                │  │  │      demo.css
                │  │  │
                │  │  ├─js
                │  │  │      outlook2.js
                │  │  │      validateExtends.js
                │  │  │
                │  │  └─themes
                │  │      │(略..)
                │  │        
                │  │      
                │  │      
                │  └─image
                │          default_portrait.png
                │
                └─templates
                        friendList.html
                        intro.html
                        main.html
```

#### 项目文件说明-`Spring boot启动类`
```
MemoApplication.java
```

#### 项目文件说明-`数据库文件`
```
memo.sql
```

#### 项目文件说明-`EasyUI 前端框架`
```
easyui/
```

#### 项目文件说明-`默认头像`
```
default_portrait.png
```


 
*🙂 Look forward to your contribution ! If you need any help, please contact me ~ QQ : 3083968068*
---
title: 'Java web项目:学生管理系统'
date: 2019-06-12 19:04:40
tags: [JSP,Servlet,jQuery,Ajax,EasyUI,MySQL]
---

## Student Information Management System
- *我的第一个`Java web`项目 （づ￣3￣）づ╭❤～*
- *赶紧来学习吧 ! 期待你的`issues`哟(っ•̀ω•́)っ✎⁾⁾~ 该项目的GitHub仓库地址 : https://github.com/YUbuntu0109/SMS*

### 项目概述

#### 项目阶段介绍
- *第一阶段：+信息管理功能 :white_check_mark:*
- *第二阶段：+成绩管理功能 :x:*

#### 用户权限介绍
- *管理员 : 具有所有管理模块的权限*
- *教师 : 具有学生管理信息模块的所有权限,但在教师信息管理模块中只具有查询并修改个人信息的权限*
- *学生 : 只具有查询并修改个人信息的权限*

*设置权限的核心示例代码如下 :*
```java
// 用户权限设置: 如果当前用户类型为教师,则将其权限设置为仅能查询个人信息
if (userType == 3) {
    TeacherInfo currentTeacherInfo = (TeacherInfo) request.getSession().getAttribute("userInfo");
	teacherInfo.setId(currentTeacherInfo.getId());
}

// 获取分页后的教师列表信息
List<TeacherInfo> teacherList = teacherDao.getTeacherList(teacherInfo, new Paging(currentPage, pageSize));
```


### 系统截图
- *登录页面*

![](https://raw.githubusercontent.com/YUbuntu0109/Student-Information-Management-System/master/demonstration_picture/Student_Information_Management_System01-LoginInterface.PNG)

- *系统主页*

![](https://raw.githubusercontent.com/YUbuntu0109/Student-Information-Management-System/master/demonstration_picture/Student_Information_Management_System01-MainInterface.PNG)

- *学生信息管理页面*

![](https://raw.githubusercontent.com/YUbuntu0109/Student-Information-Management-System/master/demonstration_picture/Student_Information_Management_System01-StudentInfoInterface.PNG)


### 项目结构
```
│
└─student_information_management_system
    │       
    │
    ├─build
    │  └─classes
    │      │  databaseConfig.properties
    │                                                                                       
    │
    ├─database
    │      SMS.sql
    │
    ├─src
    │  │  databaseConfig.properties
    │  │
    │  └─pers
    │      └─huangyuhui
    │          └─sms
    │              ├─dao
    │              │      AdminDao.java
    │              │      BasicDao.java
    │              │      ClazzDao.java
    │              │      StudentDao.java
    │              │      TeacherDao.java
    │              │
    │              ├─filter
    │              │      LoginFilter.java
    │              │
    │              ├─model
    │              │      AdminInfo.java
    │              │      ClazzInfo.java
    │              │      Paging.java
    │              │      StudentInfo.java
    │              │      TeacherInfo.java
    │              │
    │              ├─servlet
    │              │      ClazzManagementServlet.java
    │              │      LoginServlet.java
    │              │      OutVerifiCodeServlet.java
    │              │      PersonalManagementServlet.java
    │              │      PhotoServlet.java
    │              │      StuManagementServlet.java
    │              │      SysMainInterfaceServlet.java
    │              │      TeacherManagementServlet.java
    │              │
    │              └─util
    │                      CreateVerifiCodeImage.java
    │                      DbConfig.java
    │                      DbUtil.java
    │                      StringUtil.java
    │
    └─WebContent
        │  index.jsp
        │  refresh.jsp
        │
        ├─easyui
        │  │
        │  ├─css       
        │  │
        │  ├─js
        │  │     
        │  └─themes
        │      
        │
        ├─h-ui
        │  │
        │  ├─css
        │  │      
        │  ├─images
        │  │
        │  ├─js
        │  │       
        │  ├─lib
        │  │
        │  └─skin
        │     
        │
        ├─META-INF
        │      MANIFEST.MF
        │
        ├─resource
        │  └─image
        │          default_portrait.jpg
        │
        └─WEB-INF
            │  web.xml
            │
            ├─lib
            │      commons-beanutils-1.8.3.jar
            │      commons-collections-3.2.1.jar
            │      commons-fileupload-1.2.1.jar
            │      commons-io-1.4.jar
            │      commons-lang-2.5.jar
            │      commons-logging-1.1.1.jar
            │      ezmorph-1.0.6.jar
            │      FilelLoad.jar
            │      json-lib-2.3-jdk15.jar
            │      jsonplugin-0.34.jar
            │      jstl.jar
            │      mysql-connector-java-8.0.11.jar
            │      standard.jar
            │
            └─view
                │  login.jsp
                │
                ├─class
                │      classList.jsp
                │
                ├─error
                │      404.jsp
                │      500.jsp
                │
                ├─management
                │      personalView.jsp
                │
                ├─student
                │      studentList.jsp
                │
                ├─system
                │      main.jsp
                │      welcome.jsp
                │
                └─teacher
                        teacherList.jsp
```

#### 项目文件说明-`数据库文件`
```
SMS.sql
```

#### 项目文件说明-`数据库配置信息`
```
databaseConfig.properties
```

#### 项目文件说明-`H-ui 前端框架`
```
h-ui/
```

#### 项目文件说明-`EasyUI 前端框架`
```
easyui/
```


### 数据库ER图

![](https://raw.githubusercontent.com/YUbuntu0109/Student-Information-Management-System/master/demonstration_picture/sms_er.png)




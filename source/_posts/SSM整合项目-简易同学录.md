---
title: 'SSM整合项目:简易同学录'
date: 2019-06-12 19:06:17
tags: [JSP,SSM,jQuery,Ajax,EasyUI,MySQL]
---

## A Simple Alumnn Book
- *我的第一个`SSM`小项目 （づ￣3￣）づ╭❤～*
- *赶紧来学习吧 ! 期待你的`issues`哟(っ•̀ω•́)っ✎⁾⁾~ 该项目地址 : https://github.com/YUbuntu0109/SSM-CURD-Alumni*

### 项目概述
- *一个简单的,基于SSM的同学录小项目,通过本项目可以学习SSM的整合及CURD操作的基本思路,同时也可以帮助你学习Intellij IEAD开发利器的使用哟,可以说非常适合初学SSM的同学啦~*

*代码注释非常详细,思路也很清晰 :*
```java

    @Autowired //注入业务对象
    private FriendService friendService;

    //存储预返回给页面的对象数据
    private Map<String, Object> result = new HashMap<>();
    
   /**
     * @description: 分页查询: 获取所有好友信息,分页后返回给页面
     * @param: page 当前页数
     * @param: rows 每页行数
     * @date: 2019-06-06 10:10 AM
     * @return: java.util.List<pers.huangyuhui.ssm.crud.bean.Friend>
     */
    @RequestMapping("/getFriendList")
    @ResponseBody //将Map转化为JSON数据
    public Map<String, Object> getFriendlist(Integer page, Integer rows) {

        //设置每页的记录数
        PageHelper.startPage(page, rows);
        //获取所有好友信息
        List<Friend> list = friendService.selectAll();
        //封装查询结果
        PageInfo<Friend> pageInfo = new PageInfo<>(list);
        //获取总记录数
        long total = pageInfo.getTotal();
        //获取当前页数据列表
        List<Friend> friendList = pageInfo.getList();
        //存储对象数据
        result.put("total", total);
        result.put("rows", friendList);

        return result;
    }
```



### 项目截图
- *欢迎页面*

![](https://raw.githubusercontent.com/YUbuntu0109/SSM-CURD-Alumni/master/demonstration_picture/SSM-CURD-Alumni01.PNG)

- *好友列表*

![](https://raw.githubusercontent.com/YUbuntu0109/SSM-CURD-Alumni/master/demonstration_picture/SSM-CURD-Alumni02.PNG)



### IDEA截图

- *目录结构*

![](https://raw.githubusercontent.com/YUbuntu0109/SSM-CURD-Alumni/master/demonstration_picture/Intellij-IDEA-Project-Alumni.PNG)

- *目录类型(`重点呦:IDEA可不同与Eclipse !`)*

![](https://raw.githubusercontent.com/YUbuntu0109/SSM-CURD-Alumni/master/demonstration_picture/Intellij-IDEA-Folder-Alumni.PNG)

- *`Jar`包依赖关系*

![](https://raw.githubusercontent.com/YUbuntu0109/SSM-CURD-Alumni/master/demonstration_picture/SSM-CRUD-Alumni-Jar.png)



### 项目结构
```
└─SSM-CURD-Alumni
    │  LICENSE
    │  README.md
    │
    ├─database
    │      ssm.sql
    │
    ├─demonstration_picture
    │      Intellij-IDEA-Folder-Alumni.PNG
    │      Intellij-IDEA-Project-Alumni.PNG
    │      SSM-CRUD-Alumni-Jar.png
    │      SSM-CURD-Alumni01.PNG
    │      SSM-CURD-Alumni02.PNG
    │
    └─ssm_crud2
        │  pom.xml
        │  ssm_crud_alumni.iml
        │
        ├─.idea
        │  │(略..)  
        │    
        │
        └─src
            └─main
                ├─java
                │  └─pers
                │      └─huangyuhui
                │          └─ssm
                │              └─crud
                │                  ├─bean
                │                  │      Friend.java
                │                  │
                │                  ├─controller
                │                  │      FriendController.java
                │                  │
                │                  ├─dao
                │                  │      FriendMapper.java
                │                  │
                │                  └─service
                │                      │  FriendService.java
                │                      │
                │                      └─impl
                │                         FriendServiceImpl.java
                │
                ├─resource
                │  ├─database-conf
                │  │      c3p0.properties
                │  │
                │  ├─mapper
                │  │      FriendMapper.xml
                │  │
                │  ├─mybatis-conf
                │  │      mybatis-config.xml
                │  │
                │  └─spring-conf
                │          applicationContext.xml
                │          springmvc-config.xml
                │
                └─webapp
                    │  friendInfo.jsp
                    │  index.jsp
                    │
                    ├─static
                    │  └─easyui
                    │      │(略..)
                    │      
                    │
                    └─WEB-INF
                            web.xml
```


#### 项目结构说明-`数据库文件`
```
database/ssm.sql
```

#### 项目结构说明-`数据库配置信息`
```
ssm_crud2/src/main/resource/database-conf/c3p0.properties
```

#### 项目结构说明-`EasyUI Framework`
```
ssm_crud2/src/main/webapp/static/easyui/
```

#### 项目结构说明-`Spring 核心配置文件`
```
ssm_crud2/src/main/resource/spring-conf/applicationContext.xml
```

#### 项目结构说明-`Spring MVC 核心配置文件`
```
ssm_crud2/src/main/resource/spring-conf/springmvc-config.xml
```

#### 项目结构说明-`MyBatis 核心配置文件`
```
ssm_crud2/src/main/resource/mybatis-conf/mybatis-config.xml
```

#### 项目结构说明-`Mapper 接口映射文件`
```
ssm_crud2/src/main/resource/mapper/FriendMapper.xml
```

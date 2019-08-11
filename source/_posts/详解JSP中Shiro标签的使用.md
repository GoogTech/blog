---
title: 详解JSP中Shiro标签的使用
date: 2019-08-11 07:42:13
tags: [JSP,Shiro]
---

## 学习笔记 : 详解JSP中Shiro标签的使用
*Apache Shiro provides a Subject-aware JSP/GSP tag library that allows you to control your JSP, JSTL or GSP page output based on the current Subject’s state. This is quite useful for personalizing views based on the identity and authorization state of the current user viewing the web page. For more information, please refer to : http://shiro.apache.org/web.html#Web-taglibrary*

### Tag Library Configuration
*The Tag Library Descriptor (TLD) file is bundled in shiro-web.jar in the `META-INF/shiro.tld` file. To use any of the tags, add the following line to the top of your JSP page (or wherever you define page directives) :*
```xml
<%@ taglib prefix="shiro" uri="http://shiro.apache.org/tags" %>
```
* We’ve used the shiro prefix to indicate the shiro tag library namespace, but you can assign whatever name you like.


### shiro : authenicated
*表示认证已通过,但不包括`remember me`登录的*
```xml
<shiro:authenticated>
    <label>用户身份验证已通过</label>
</shiro:authenticated>
```

*说明 : 只有已通过用户认证,但不是通过记住我(`remember me`)浏览才会看到标签内的内容*


### shiro : guest 
*表示是游客身份,没有登录*
```xml
<shiro:guest>
    <label>您当前是游客,</label><a href="/login.jsp" >请登录</a>
</shiro:guest>
```

*说明 : 只有是没有登录过,以游客的身份浏览才会看到标签内的内容*


### shiro : hasAnyRoles
*表示拥有这些角色中其中一个*
```xml
<shiro:hasAnyRoles name="admin,user">
    <label>这是拥有admin或者是user角色的用户</label>
</shiro:hasAnyRoles>
```

*说明 : 只有成功登录后,且具有admin或者user角色的用户才会看到标签内的内容. name属性中可以填写多个角色名称，以逗号( , )分隔*


### shiro : hasPermission
*表示拥有某一权限*
```xml
<shiro:hasPermission name="admin:add">
    <label>这个用户拥有admin:add的权限</label>
</shiro:hasPermission>
```

*说明 : 只有成功登录后,且具有admin:add权限的用户才可以看到标签内的内容,name属性中只能填写一个权限的名称*


### shiro : hashRole
*表示拥有某一角色*
```xml
<shiro:hasRole name="admin">
    <label>这个用户拥有的角色是admin</label>
</shiro:hasRole>
```

*说明 : 只有成功登录后,且具有admin角色的用户才可以看到标签内的内容,name属性中只能填写一个角色的名称*


### shiro : lacksPermission
*表示不拥有某一角色*
```xml
<shiro:lacksPermission name="admin:delete">
    <label>这个用户不拥有admin:delete的权限</label>
</shiro:lacksPermission>
```

*说明 : 只有成功登录后,且不具有admin:delete权限的用户才可以看到标签内的内容,name属性中只能填写一个权限的名称*


### shiro : lacksRole
*表示不拥有某一角色*
```xml
<shiro:lacksRole name="admin">
    <label>这个用户不拥有admin的角色</label>
</shiro:lacksRole>
```

*说明 : 只有成功登录后，且不具有admin角色的用户才可以看到标签内的内容,name属性中只能填写一个角色的名称*


### shiro : notAuthenticated
*表示没有通过验证*
```xml
<shiro:notAuthenticated>
    <label>用户身份验证没有通过</label>
</shiro:notAuthenticated>
```

*说明 : 只有没有通过验证的才可以看到标签内的内容,包括通过记住我(`remember me`)登录的*


### shiro : principal
*表示用户的身份 : 取值取的是你登录的时候,在Realm实现类中的`SimpleAuthenticationInfo(Object principal, Object hashedCredentials, ByteSource credentialsSalt, String realmName)`放的第一个参数哟~ 例如 :*
```java
return new SimpleAuthenticationInfo(user, user.getPassword(), ByteSource.Util.bytes(user.getUsername()), this.getName());
```

1. *如果第一个放的是username或者是一个值,那么就可以直接用*
```xml
<!--取到username-->
<shiro: principal/>
```

2. *如果第一个参数放的是对象,比如放User对象. 那么如果要取其中某一个值,可以通过property属性来指定*
```xml
<!--需要指定property-->
<shiro:principal property="username"/>
```


### shiro : user
*表示已登录*
```xml
<shiro:user>
    <label>欢迎[<shiro:principal/>],</label><a href="/logout.jsp">退出</a>
</shiro:user>
```

*说明 : 只有已经登录(包含通过记住我(`remember me`)登录的)的用户才可以看到标签内的内容. 一般和标签`shiro : principal`一起用,来做显示用户的名称*


### 标签嵌套
*shiro的jsp标签可以嵌套使用,可以根据业务的具体场景进行使用. 例如一个按钮需要排除不是admin或user角色的用户才可以显示,则可以像如下这样实现 :*
```xml
<shiro:lacksRole name="admin">
    <shiro:lacksRole name="user"> 
        <label>这个用户不拥有admin或user的角色</label>
    </shiro:lacksRole>
</shiro:lacksRole>
```



*🙏参考链接(获益匪浅,非常感谢) :*
* https://blog.csdn.net/yaodieteng1510/article/details/79992247

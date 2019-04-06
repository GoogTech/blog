---
title: Servlet同版本对应的Tomcat及不同版本的web.xml写法
date: 2019-04-05 14:34:34
tags: [xml,Servlet,JSP,Tomcat,JDK]
---

### `Tomcat` 实现了`Java Servlet`规范,`Java Servlet Pages`技术,不同版本的`Tomcat`支持不同的`Servlet`及`JSP`规范.

- *最新示例图如下所示 :*
![ ](Servlet同版本对应的Tomcat及不同版本的web-xml写法/different-version.PNG)


### 不同版本的`web.xml`写法如下 :
- *web.xml v2.3*
```xml
<?xml version="1.0" encoding="ISO-8859-1"?>  
<!DOCTYPE web-app PUBLIC "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN" "http://java.sun.com/dtd/web-app_2_3.dtd">  
<web-app>  
   <!-- ... -->
</web-app>
```

- *web.xml v2.4*
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<web-app id="WebApp_9" version="2.4" xmlns="http://java.sun.com/xml/ns/j2ee"   
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"  
        xsi:schemaLocation="http://java.sun.com/xml/ns/j2ee http://java.sun.com/xml/ns/j2ee/web-app_2_4.xsd">  
</web-app>
```

- *web.xml v2.5*
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<web-app xmlns="http://java.sun.com/xml/ns/javaee"  
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"  
        xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd" version="2.5">
</web-app>
```

- *web.xml 3.0*
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<web-app  
        version="3.0"  
        xmlns="http://java.sun.com/xml/ns/javaee"  
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"  
        xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd">  
</web-app>
```

- *web.xml 4.0*
```xml
<?xml version="1.0" encoding="UTF-8"?> 
<!DOCTYPE xml>
<web-app  
        version="4.0"
        xmlns="http://xmlns.jcp.org/xml/ns/javaee"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd">
</web-app>
```
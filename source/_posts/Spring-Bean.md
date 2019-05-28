---
title: 'Spring:Bean'
date: 2019-05-14 22:04:41
tags: [Spring]
---

## 学习笔记 : Spring Bean

### Bean 的配置
*简介 : Spring可以被看成一个大型工厂,其作用是`生产`和`管理`Spring容器中的`Bean`,前提是需要在Sring配置文件中进行配置. Spring容器支持`XML`和`Properties`两种格式的配置文件.其前者最为常用哟! 在配置文件中,通常为一个Bean配置`id`或`name`和`class`两个属性即可,注意的是如果Bean中未指定id和name,则Spring会将class值当做id使用.*



### Bean 的实例化
*简介 : 在Spring中,想要使用容器中的Bean时需要先将其实例化,其实例化的方式有三种,如下所示.*
1. 构造器实例化(`最常用`) : Spring容器通过Bean对应类中默认的无参构造方法来实例化Bean.
2. 静态工厂方式实例化 : 将Bean配置中的class属性指定静态工厂类,然后使用`factory-method`属性来指定所创建的静态工厂方法.
3. 实例工厂方式实例化 : 将需要实例化的Bean通过`factory-bean`属性指向配置的实例工厂,然后使用`factory-method`属性确定使用工厂中的具体方法.


##### Bean 的实例化案例
*通过一个示例程序来演示实例化Bean的三种方式.*

1. *Bean类*
```java
package pers.huangyuhui.spring.bean.instance;

//构造器实例化
public class Bean1 {

}

package pers.huangyuhui.spring.bean.instance;

//静态工厂方式实例化  
public class Bean2 {

}

package pers.huangyuhui.spring.bean.instance;

//实例工厂方式实例化
public class Bean3 {

}
```

2. *MyBean2Factory.java : 静态工厂*
```java
package pers.huangyuhui.spring.bean.factory;

import pers.huangyuhui.spring.bean.instance.Bean2;

/**
 * @ClassName: MyBean2Factory
 * @Description: 静态工厂方式实例化
 * @author: HuangYuhui
 * @date: May 24, 2019 2:08:00 PM
 *
 */
public class MyBean2Factory {

	public static Bean2 createBean() {
		return new Bean2();
	}
}
```

3. *MyBean3Factory.java : 实例工厂*
```java
package pers.huangyuhui.spring.bean.factory;

import pers.huangyuhui.spring.bean.instance.Bean3;

/**
 * @ClassName: MyBean3Factory
 * @Description: 实例工厂方式实例化
 * @author: HuangYuhui
 * @date: May 24, 2019 2:16:03 PM
 *
 */
public class MyBean3Factory {

	public MyBean3Factory() {
        System.out.println("this is constructor ~");
	}

	public Bean3 createBean() {
		return new Bean3();
	}
}
```

4. *applicationContext.xml : Spring核心配置文件*
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
	
	<!-- 构造器实例化 -->
	<bean id="bean1" class="pers.huangyuhui.spring.bean.instance.Bean1"/>

	<!-- 静态工厂方式实例化 -->
	<!-- factory-method: 指定工厂方法 -->
	<bean id="bean2" class="pers.huangyuhui.spring.bean.factory.MyBean2Factory" factory-method="createBean"/>

	<!-- 实例工厂方式实例化 -->
	<!-- factory-bean:指定配置的实例工厂 -->
	<bean id="myBean3Factory" class="pers.huangyuhui.spring.bean.factory.MyBean3Factory"/>
	<bean id="bean3" factory-bean="myBean3Factory" factory-method="createBean"/>
	
</beans>
```

5. *InstanceTest.java : 测试类*
```java
package pers.huangyuhui.spring.bean.instance.test;

import org.junit.BeforeClass;
import org.junit.Ignore;
import org.junit.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

import pers.huangyuhui.spring.bean.instance.Bean1;

/**
 * @ClassName: InstanceTest
 * @Description: 测试
 * @author: HuangYuhui
 * @date: May 24, 2019 1:57:41 PM
 *
 */
public class InstanceTest {

	private static ApplicationContext applicationContext;

	@BeforeClass
	public static void init() {
		applicationContext = new ClassPathXmlApplicationContext("applicationContext.xml");
	}

	@Test
	@Ignore
	// 构造器实例化
	public void instanceTest1() {
		Bean1 bean1 = applicationContext.getBean(Bean1.class);

		// result : pers.huangyuhui.spring.bean.instance.Bean1@59402b8f
		System.out.println(bean1);
	}

	@Test
	@Ignore
	// 静态工厂方式实例化
	public void instanceTest2() {

		// result : pers.huangyuhui.spring.bean.instance.Bean2@70ed52de
		System.out.println(applicationContext.getBean("bean2"));
	}

	@Test
	@Ignore
	// 实例工厂方式实例化
	public void instanceTest3() {

		// result :
		// this is constructor ~
		// pers.huangyuhui.spring.bean.instance.Bean3@70ed52de
		System.out.println(applicationContext.getBean("bean3"));
	}

}
```



### Bean 的作用域
*简介 : 在Spring 4.3中为Bean定义了七种作用域,如下所示.*
1. Singleton(`单例`) : 使用Singleton定义的Bean在Spring容器中将只有一个实例,也就是说,无论有多少个Bean引用它,始终指向同一个对象,其也是Spring默认的作用域.
2. prototype(`原型`) : 每次通过Spring容器获取prototype定义的Bean时,容器都将创建一个新的Bean实例.
3. request : ..
4. session : ..
5. globalSession : ..
6. application : ..
7. websocket : ..


#### Bean 的作用域案例
*下面通过简单程序来分别演示`singleton`和`prototype`作用域*

1. *Bean类*
```java
package pers.huangyuhui.spring.bean.scope;

//Bean的作用域: singleton 
public class Bean4 {
}


package pers.huangyuhui.spring.bean.scope;

//Bean的作用域: prototype
public class Bean5 {
}
```

2. *Spring核心配置文件*
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

	<!-- singleton(default) -->
	<bean id="bean4" class="pers.huangyuhui.spring.bean.scope.Bean4" scope="singleton"/>
	<!-- prototype -->
	<bean id="bean5" class="pers.huangyuhui.spring.bean.scope.Bean5" scope="prototype"/>

</beans>
```

3. *测试类*
```java
package pers.huangyuhui.spring.bean.scope.test;

import org.junit.BeforeClass;
import org.junit.Ignore;
import org.junit.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

/**
 * @ClassName: ScopeTest
 * @Description: 测试
 * @author: HuangYuhui
 * @date: May 24, 2019 2:53:10 PM
 *
 */
public class ScopeTest {

	private static ApplicationContext applicationContext;

	@BeforeClass
	public static void init() {
		applicationContext = new ClassPathXmlApplicationContext("applicationContext.xml");
	}

	@Test
	@Ignore
	// Bean的作用域: singleton
	public void singletonTest() {
		// result:
		// pers.huangyuhui.spring.bean.scope.Bean4@7c137fd5
		// pers.huangyuhui.spring.bean.scope.Bean4@7c137fd5
		System.out.println(applicationContext.getBean("bean4"));
		System.out.println(applicationContext.getBean("bean4"));
	}

	@Test
	@Ignore
	// Bean的作用域: prototype
	public void prototypeTest() {
		// result: pers.huangyuhui.spring.bean.scope.Bean5@3e08ff24
		System.out.println(applicationContext.getBean("bean5"));
	}

}
```



### Bean 的装配方式
*简介 : Bean的装配方式可以理解为依赖注入,Bean的装配方式既Bean依赖注入的方式. Spring容器支持多种形式的Bean的装配方式,如基于`XML`的装配,基于`Annotation`的装配等..*


#### 基于XML的装配
*Spring提供了两种基于XML的装配方式: 设值注入(`Setter Injection`)和构造注入(`Constructor Injection`). 在Spring实例化Bean的过程中,`Spring首先会调用Bean的默认构造方法来实例化Bean对象,然后通过反射的方式调用setter方式来注入属性值`.因此,设值注入要求一个Bean必须满足一下两点要求.*
1. Bean类必须提供一个默认的无参构造方法.
2. Bean类必须为需要注入的属性提供对应的`setter`方法.

*下面通过一个简单示例程序来展示基于XML的Bean的装配方式.*
1. *User.java : Java bean*
```java
package pers.huangyuhui.spring.assemble.bean;

import java.util.List;

/**
 * @ClassName: User
 * @Description: 用户信息
 * @author: HuangYuhui
 * @date: May 24, 2019 3:37:58 PM
 *
 */
public class User {

	private String name;
	private List<String> phone;

	public User() {

	}

	public User(String name, List<String> phone) {
		super();
		this.name = name;
		this.phone = phone;
	}

	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}

	public List<String> getPhone() {
		return phone;
	}

	public void setPhone(List<String> phone) {
		this.phone = phone;
	}

	@Override
	public String toString() {
		return "User [name=" + name + ", phone=" + phone + "]";
	}

}
```

2. *applicationContext.xml : Spring核心配置文件*
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
	
	 <!-- 利用构造注入方式装配User实例 -->
	 <bean id="user1" class="pers.huangyuhui.spring.assemble.bean.User">
	 	<constructor-arg index="0" value="YUbuntu0109-1"/>
	 	<constructor-arg index="1">
	 		<list>
	 			<value>"15111111111"</value>
	 			<value>"15211111111</value>
	 		</list>
	 	</constructor-arg>
	 </bean>
	 <!-- 使用设值注入方式装配User实例 -->
	 <bean id="user2" class="pers.huangyuhui.spring.assemble.bean.User">
	 	<property name="name" value="YUbuntu0109-2"/>
	 	<property name="phone">
	 		<list>
	 			<value>"13111111111"</value>
	 			<value>"18111111111"</value>
	 		</list>
	 	</property>
	 </bean>
</beans>
```

3. *AssembleTest.java : 测试类*
```java
package pers.huangyuhui.spring.assemble.test;

import org.junit.BeforeClass;
import org.junit.Ignore;
import org.junit.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

import pers.huangyuhui.spring.assemble.controller.UserController;

/**
 * @ClassName: AssembleTest
 * @Description: 测试
 * @author: HuangYuhui
 * @date: May 24, 2019 3:49:42 PM
 *
 */
public class AssembleTest {

	private static ApplicationContext applicationContext;

	@BeforeClass
	public static void init() {
		applicationContext = new ClassPathXmlApplicationContext("applicationContext.xml");
	}

	@Test
	// @Ignore
	// 测试基于XML的Bean配置
	public void xmlBeanAssembleTest() {

		// result: User [name=YUbuntu0109-1, phone=["15111111111", "15211111111]]
		System.out.println(applicationContext.getBean("user1"));
		// result: User [name=YUbuntu0109-2, phone=["13111111111", "18111111111"]]
		System.out.println(applicationContext.getBean("user2"));
	}

}
```


#### 基于Annotation的装配
*简介 : 通过注解(`Annotation`)来实现Bean的装配工作可以解决XML配置文件过于臃肿的问题,且便于后期维护. 其常用注解如下.*
1. `@Repository` : 用于将数据访问层(DAO层)的类标识为Spring中的Bean.
2. `@Service` : 通常作用在业务层(Service层),用于将业务层的类标识为Spring中的Bean.
3. `@Controller` : 通过作用在控制层(如Spring MVC的Controller),用于将控制层的类标识为Spring中的Bean.
4. `@Autowired` : 用于对Bean的属性变量,属性的setter方法及构造方法进行标注,配合对用的注解处理器来完成Bean的自动配置工作.

*下面通过一个简单案例来演示如何通过这些注解来装配Bean.*
1. *UserDao.java*
```java
package pers.huangyuhui.spring.assemble.dao;

public interface UserDao {

	public void save();
}
```

2. *UserDaoImple.java*
```java
package pers.huangyuhui.spring.assemble.dao.impl;

import org.springframework.stereotype.Repository;

import pers.huangyuhui.spring.assemble.dao.UserDao;

/**
 * @ClassName: UserDaoImpl
 * @Description: UserDao的实现类
 * @author: HuangYuhui
 * @date: May 24, 2019 4:13:53 PM
 *
 */
@Repository("userDao") // 将UserDaoImpl类标识为Spring中的Bean
public class UserDaoImpl implements UserDao {

	@Override
	public void save() {
		System.out.println("UserDao : Save function ~");
	}

}
```
- *其中使用`@Respository`注解将UserDaoImpl类标识为Spring中的Bean,其写法相当于配置文件中的`<bean id="userDao" class="pers.huangyuhui.spring.assemble.dao.impl.UserDaoImpl"/>`*

3. *UserService.java*
```java
package pers.huangyuhui.spring.assemble.dao;

public interface UserService {

	public void save();
}
```

4. *UserServiceImpl.java*
```java
package pers.huangyuhui.spring.assemble.dao.impl;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import pers.huangyuhui.spring.assemble.dao.UserDao;
import pers.huangyuhui.spring.assemble.dao.UserService;

/**
 * @ClassName: UserServiceImpl
 * @Description: UserService的实现类
 * @author: HuangYuhui
 * @date: May 24, 2019 4:20:15 PM
 *
 */
@Service // 将UserServiceImpl类标识为Spring中的Bean
public class UserServiceImpl implements UserService {

	@Autowired // 自动配置Bean
	private UserDao userDao;

	@Override
	public void save() {
		userDao.save();
		System.out.println("UserService : Save function ~");
	}

}
```

5. *UserController.java*
```java
package pers.huangyuhui.spring.assemble.controller;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Repository;

import pers.huangyuhui.spring.assemble.dao.UserService;

/**
 * @ClassName: UserController
 * @Description: TODO
 * @author: HuangYuhui
 * @date: May 24, 2019 4:33:56 PM
 *
 */
@Repository /// 将UserController类标识为Spring中的Bean
public class UserController {

	@Autowired // 自动配置Bean
	private UserService userService;

	public void save() {
		userService.save();
		System.out.println("UserController : Save function ~");
	}

}
```

6. *AssembleTest.java : 测试类*
```java
package pers.huangyuhui.spring.assemble.test;

import org.junit.BeforeClass;
import org.junit.Ignore;
import org.junit.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

import pers.huangyuhui.spring.assemble.controller.UserController;

/**
 * @ClassName: AssembleTest
 * @Description: 测试
 * @author: HuangYuhui
 * @date: May 24, 2019 3:49:42 PM
 *
 */
public class AssembleTest {

	private static ApplicationContext applicationContext;

	@BeforeClass
	public static void init() {
		applicationContext = new ClassPathXmlApplicationContext("applicationContext.xml");
	}

	@Test
	public void annotationAssembleTest() {
		UserController userController = (UserController) applicationContext.getBean("userController");
		userController.save();
	}

}
```

7. *Spring核心配置文件*
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:context="http://www.springframework.org/schema/context"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.3.xsd">
	 
	 <!-- 扫描指定包下的所有Bean类,进行注解解析 -->
	 <context:component-scan base-package="pers.huangyuhui.spring.assemble"/>
	 
</beans>
```

8. *程序运行结果*
```
UserDao : Save function ~
UserService : Save function ~
UserController : Save function ~
```
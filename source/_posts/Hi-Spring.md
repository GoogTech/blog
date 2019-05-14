---
title: Hi Spring ~
date: 2019-05-09 21:01:53
tags: [Spring]
---

## 学习笔记 : 初识 Spring 


### Spring 简介
*`Spring`是由Rod Johnson组织和开发的一个分层的Java SE/EE full-stack(一站式)轻量级开源框架,它以`IoC`(Inversoin of Control,控制反转)和`AOP`(Aspect Oriented Programming,面向切面编程)为内核,使用基本的JavaBean来完成以前只可能由`EJB`(Enterprise Java Beans,Java企业Bean)完成的工作.取代了EJB的臃肿,低效的开发模式哟 !*



### Spring 框架优点
*Spring 具有简单,可测试和松耦合等特点,从这个角度出发,Spring可适用于服务器端/任何Java应用的开发.*
1. 非侵入式设计
2. 方便耦合,简化开发
3. 支持AOP
4. 支持声明式事务处理
5. 方便程序的测试
6. 方便集成各种优秀的框架
7. 降低Java EE API的使用难度



### Spring 核心容器
*Spring框架的主要功能是通过其核心容器来实现.Spring框架提供了两种核心容器,分别是`BeanFatory`和`ApplicatoinContext`.*

#### BeanFactory
*`BeanFactory`为基础类型的Ioc容器,简单的说就是一个管理Bean的工厂,它主要负责初始化各种Bean,并调用它们的生命周期方法.*

#### ApplicationContext
*`ApplicationContext`是`BeanFactory`的子接口,也称为应用上下文,不仅包含了BeanFactory的所有功能,还添加了对国际化,资源访问,事件传播等方面的支持.有两种创建ApplicatoinContext接口实例的方法.*

1. 通过`ClassPathXmlApplicationContext`创建
*通过类路径classPath中寻找指定的XML配置文件,找到并装载完成ApplicationContext的实例化工作.*

2. 通过`FileSystemXmlApplicatoinContext`创建
*通过指定的文件系统路径(绝对路径)中寻找指定的XML配置文件,找到并装载完成ApplicationContext的实例化工作.*



### 依赖注入
*依赖注入的作用就是在使用Spring框架创建对象时,动态地将其所依赖的对象注入`Bean`组件中,其实现方式通常有两种,一种是属性`setter方法`注入,另一种是`构造方法`注入.*

#### 属性setter方法注入
*指Spring容器使用setter方法注入被依赖的实例.通过调用无参的构造器或无参静态工厂方法实例化Bean后,调用该Bean的setter方法,即可实现基于setter方法的依赖注入.*

#### 构造方法注入
*指Spring容器使用的构造方法注入被依赖的实例.基于构造方法的依赖注入通过带参数的构造方法来实现,每个参数代表着一个依赖.*



### Spring 简单示例程序
1. *创建一个简单的Java bean*
```java
package pers.huangyuhui.spring.bean;

public class HiSpring5 {

	private String name;

	public HiSpring5() {
		System.out.println("The Constructor  be called !");
	}

	public void setName(String name) {
		System.out.println("setName(String name) method be called !");
		this.name = name;
	}

	public void ouputName() {
		System.out.println("ouputName() method: my name is " + name + " !");
	}
}
```

2. *创建Spring的配置文件: applicationContext.xml*
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
	
	<!--
	id: IOC容器中Bean的唯一标识
	class: 通过反射机制在IOC容器中创建Bean(所以要求Bean中必须有无参的构造器).
	-->
	<bean id="SpringTestID" class="pers.huangyuhui.spring.bean.HiSpring5">
		<!-- 初始化HiSpring类中的属性名为name的值 -->
		<property name="name" value="Spring"></property>
	</bean>

</beans>
```

3. *测试类*
```java
package pers.huangyuhui.spring.bean.test;

import org.junit.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

import pers.huangyuhui.spring.bean.HiSpring5;

public class Spring5Test {

	@Test
	public void test() {

		
        // 创建Spring的IOC对象
		ApplicationContext applicationContext = new ClassPathXmlApplicationContext("applicationContext.xml");

		// 从IOC容器中获取Bean实例
		HiSpring5 hiSpring5 = (HiSpring5) applicationContext.getBean("SpringTestID");
        
		hiSpring5.ouputName();

	}

}
```

4. *程序运行结果如下*
```java
May 14, 2019 4:23:28 PM org.springframework.context.support.ClassPathXmlApplicationContext prepareRefresh
INFO: Refreshing org.springframework.context.support.ClassPathXmlApplicationContext@a2431d0: startup date [Tue May 14 16:23:28 CST 2019]; root of context hierarchy
May 14, 2019 4:23:28 PM org.springframework.beans.factory.xml.XmlBeanDefinitionReader loadBeanDefinitions
INFO: Loading XML bean definitions from class path resource [applicationContext.xml]

The Constructor  be called !
setName(String name) method be called !
ouputName() method: my name is Spring !
```
---
title: Spring 依赖注入
date: 2019-04-02 21:12:43
tags: [Spring]
---

## Spring framework `依赖注入篇`

*如果想区分`依赖注入`与`控制反转(IOC)`两者之间的不同,可以参考`Martin Fowler`给出的解释.查看文章: [GO](http://martinfowler.com/articles/injection.html)*

- *简单来说,依赖注入的情况如下 :*
```java
public class A {
    public void importantMethod() {
        B b = ... // get an instance of B
        b.usefulMethod();
        ...
    }
    ...
}
```
- *如果要使用`B`,类`A`必须先获得组件`B`的实例引用,若`B`是一个具体类,则可以通过`new`关键字直接创建组件`B`实例.*但是,如果`B`是接口,且有多个实现,则问题就变得复杂了,我们固然可以任意选择接口`B`的一个实现类,但这也意味着`A`的可重用性大大降低了,因为无法采用`B`的其他实现.*

- *依赖注入是这样处理此类情景的 : 接管对象的创建工作,并将该对象的引用注入需要该对象的组件.以上述例子为例,依赖注入*框架会分别创建对象`A`和对象`B`,将对象`B`注入到对象`A`中.为了能让框架进行依赖注入,程序员需要编写特定的`set`方法*或构建方法,例如: 为了能将`B`注入到`A`中,类`A`会被修改成如下形式 :*
```java
public class A {
    private B b;
    public void importantMethod() {
        // no need to worry about creating B anymore.
        // B b = ... // get an instance of B.
        b.usefulMethod();
        ...
    }
    public void setB(B b) {
        this.b = b;
    }
}
```

- *修改后的类`A`新增了一个`setter`方法,该方法将会被框架调用,以注入一个`B`的实例.由于对象依赖由依赖注入,类`A`的`importantMethod()`方法不再需要在调用`B`的`usefulMethod()`方法前去创建一个`B`的实例.*

- *当然,也可以采用`构造器方式`注入,如下所示 : (本例中,`Spring`会先创建`B`的实例,再创建实例`A`,然后把`B`注入到实例`A`中.)*
```java
public class A {
    private B b;

    public A(B b){
        this.b = b;
    }
    public void importantMethod() {
        // no need to worry about creating B anymore.
        // B b = ... // get an instance of B.
        b.usefulMethod();
        ...
    }
}
```
- **注意 : `Spring` 管理的对象称为`beans` .**

```
通过提供一个控制反转器(或者依赖注入器IOC),`Spring`为我们提供一种可以'聪明'地管理Java对象的依赖关系的方法.
其优雅之处在于 : 程序员无须了解`Spring`框架的存在,更不需要引入任何`Spring`类型.

使用`Spring`,程序几乎将所有重要对象的创建工作移交给了`Spring`,并配置如何注入依赖.`Spring`支持`XML`和`注解`
两种配置方式.此外还需要创建是一个`ApplicationContext`对象,其代表一个`Spring`控制反转容器,`org.springframework.
context.ApplicationContext`接口有多个实现,包括`ClassPathXmlApplicationContext`和`FileSystemXmlApplicationContext`.
这两个实现都需要至少一个包含`beans`信息的`XML`文件.
```

- *ClassPathXmlApplicationContext : 在类加载路径中加载配置文件.*
- *FileSystemXmlApplicationContext : 在文件系统路径中加载文件.*
  
#### 下面为从类路径中加载`applicationContext1.xml`和`applicationContext2.xml`的`ApplicationContext`创建的一个代码示例 :
```java
ApplicationContext context = new ClassPathXmlApplicationContext(
    new String[]{"applicationContext1.xml","applicationContext2.xml"})

//可以通过调用`ApplicationContext`的`getBean`方法获得对象
//getBean方法会查询`id`为`product`且类型为`Product`的`bean`对象.
Product product = context.getBean("product",Product.class);
```

- **注 : 理想情况下,我们仅需在测试代码中创建一个`ApplicationContext`,应用程序本身无需处理,对于`Spring MVC`应用,可以通过一个`Spring Servlet`来处理`ApplicationContext`,既而无须直接处理.**

- *摘抄书籍 : 《 Servlet,JSP and Spring MVC - beginner 》 ->  author: [加] Budi Kurniawan [美] Paul Deck*
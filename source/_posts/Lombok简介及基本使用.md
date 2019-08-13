---
title: Lombok简介及基本使用
date: 2019-08-13 20:46:07
tags: [Java,Lombok]
---

## 学习笔记 : Lombok简介及基本使用

### 简介
*问题引入 : 开发中经常需要写的JavaBean,都需要花时间去添加相应的getter/setter,也许还要去写构造器、equals等方法,而且需要维护,当属性多时会出现大量的getter/setter方法,这些显得很冗长也没有太多技术含量,一旦修改属性,就容易出现忘记修改对应方法的失误哟~*

*Lombok能通过注解的方式,在编译时自动为属性生成`构造器`、`getter/setter`、`equals`、`hashcode`、`toString`方法. 出现的神奇就是在源码中没有getter和setter方法,但是在编译生成的字节码文件中有getter和setter方法. 这样就省去了手动重建这些代码的麻烦,既而达到简化代码,提高开发人员的开发效率的目的*

* 优点 :
    * 能通过注解的形式自动生成构造器、getter/setter、equals、hashcode、toString等方法,提高了一定的开发效率
    * 让代码变得简洁,不用过多的去关注相应的方法
    * 属性做修改时,也简化了维护为这些属性所生成的getter/setter方法等
* 缺点 :
    * 不支持多种参数构造器的重载
    * 虽然省去了手动创建getter/setter方法的麻烦,但大大降低了源代码的可读性和完整性,降低了阅读源代码的舒适度
* 总结 : 
    * Lombok虽然有很多优点,但Lombok更类似于一种IDE插件,项目也需要依赖相应的jar包. Lombok依赖jar包是因为编译时要用它的注解,为什么说它又类似插件 ? 因为在使用时Eclipse或IntelliJ IDEA都需要安装相应的插件,`在编译器编译时通过操作AST( 抽象语法树 )改变字节码生成,变向的就是说它在改变Java语法. 它不像Spring的依赖注入或者MyBatis的ORM一样是运行时的特性,而是编译时的特性`


### 开发环境
1. *添加Maven依赖 :*
```xml
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <version>1.16.20</version>
    <scope>provided</scope>
</dependency>
```

2. *安装IDEA(`IntelliJ Lombok plugin`)插件*


### 常见异常
1. *(已添加`lombok`依赖情况下)抛出此异常,(IDEA环境)解决方案:安装`IntelliJ Lombok plugin`并启用`Enable annotation processing`*
```
Warning:(49, 8) java: lombok.javac.apt.LombokProcessor could not be initialized. Lombok will not run during this compilation: java.lang.IllegalArgumentException: com.sun.tools.javac.main.DelegatingJavaFileManager$DelegatingSJFM extends com.sun.tools.javac.main.DelegatingJavaFileManager implements javax.tools.StandardJavaFileManager
  	at lombok.javac.apt.LombokFileObjects.getCompiler(LombokFileObjects.java:148)
  	at lombok.javac.apt.InterceptingJavaFileManager.<init>(InterceptingJavaFileManager.java:40)
  	at lombok.javac.apt.LombokProcessor.placePostCompileAndDontMakeForceRoundDummiesHook(LombokProcessor.java:165)
  	at lombok.javac.apt.LombokProcessor.init(LombokProcessor.java:87)
  	at lombok.core.AnnotationProcessor$JavacDescriptor.want(AnnotationProcessor.java:87)
  	at lombok.core.AnnotationProcessor.init(AnnotationProcessor.java:140)
```

2. *JDK版本bug,解决方案:重置JDK版本,Project Structure--->Porject settings--->Project : 将`Project SDK`设置为:`9(java version"9.0.4")`, `language level`设置为`8`,详情操作请参考 : [IDEA为何多处设置JDK,有什么区别呢 ?](https://github.com/YUbuntu0109/YUbuntu0109.github.io/blob/ce0545de04d3cfc9b328a1760bb5f3c84d322362/source/_posts/%E8%AF%A6%E8%A7%A3%E5%9C%A8IDEA%E4%B8%AD%E5%A4%9A%E5%A4%84%E8%AE%BE%E7%BD%AEJDK%E5%8F%8A%E5%85%B6%E5%8C%BA%E5%88%AB.md)*
```
Warning:(9, 8) java: lombok.javac.apt.LombokProcessor could not be initialized. Lombok will not run during this compilation: java.lang.IllegalArgumentException: com.sun.tools.javac.main.DelegatingJavaFileManager$DelegatingSJFM extends com.sun.tools.javac.main.DelegatingJavaFileManager implements javax.tools.StandardJavaFileManager
  	at lombok.javac.apt.LombokFileObjects.getCompiler(LombokFileObjects.java:148)
  	at lombok.javac.apt.InterceptingJavaFileManager.<init>(InterceptingJavaFileManager.java:40)
```


### 使用Lombok前后对比
1. *一个未使用Lombok的普通JavaBean*
```java
package pers.huangyuhui.lombok.bean;

import org.apache.log4j.Logger;

public class User {

    private Integer id;
    private String name;
    private String password;
    private Logger log = Logger.getLogger(this.getClass());

    public User() {
    }

    public User(Integer id, String name, String password) {
        this.id = id;
        this.name = name;
        this.password = password;
    }

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }

    @Override
    public String toString() {
        return "User{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", password='" + password + '\'' +
                '}';
    }
}
```

2. *一个使用Lombok的普通JavaBean( 使用Lombok注解达到与上述JavaBean同样的效果 )~*
* `@Data` : 注解在类上,提供类所有属性的 getting 和 setting 方法,此外还提供了equals、canEqual、hashCode、toString方法
* `@Setter` : 注解在属性上,为属性提供 setting 方法
* `@Getter` : 注解在属性上,为属性提供 getting 方法
* `@Log4j` : 注解在类上,为类提供一个属性名为 log 的 log4j 日志对象
* `@ToString` : 注解在类上,为类提供一个 toString 方法
* `@NoArgsConstructor` : 注解在类上,为类提供一个无参的构造方法
* `@AllArgsConstructor` : 注解在类上,为类提供一个全参的构造方法

```java
package pers.huangyuhui.lombok.bean;


import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;
import lombok.ToString;
import lombok.extern.log4j.Log4j;

@Data
@Log4j
@ToString
@NoArgsConstructor
@AllArgsConstructor
public class UserWithLombok {

    private Integer id;
    private String name;
    private String password;

    public void outLogInfo() {
        log.info("the message object to log ~");
    }
}
```



*参考链接 :*
* https://github.com/rzwitserloot/lombok
* https://www.cnblogs.com/heyonggang/p/8638374.html
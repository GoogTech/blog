---
title: 详解JDK 9中的新特性
date: 2019-08-21 10:15:16
tags: [JDK]
---

## 学习笔记 : 详解JDK 9中的新特性

*由于 JDK9 中下面两个特性很重要,所有小哥我就单独分开写啦~*

* 详解JDK 9中的新特性 : [JDK 9目录结构的变化及新特性之模块化](https://github.com/YUbuntu0109/YUbuntu0109.github.io/blob/824e4435b7805308c25cf6d4bc9d12dfe57c3acb/source/_posts/JDK-9%E7%9B%AE%E5%BD%95%E7%BB%93%E6%9E%84%E7%9A%84%E5%8F%98%E5%8C%96%E5%8F%8A%E6%96%B0%E7%89%B9%E6%80%A7%E4%B9%8B%E6%A8%A1%E5%9D%97%E5%8C%96.md) 
* 详解JDK 9中的新特性 : [JDK 9新特性之REPL(JShell)](https://github.com/YUbuntu0109/YUbuntu0109.github.io/blob/5d65a860c457559e757af21b4aac423d6f7e912f/source/_posts/JDK-9%E6%96%B0%E7%89%B9%E6%80%A7%E4%B9%8BREPL-JShell.md)
  

### 接口的私有方法
*Java 8 中规定接口中的方法除了抽象方法之外,还可以定义静态和默认的方法. 一定程度上扩展了接口的功能,此时的接口更像是一个抽象类. 而在 Java 9 中,接口更加的灵活和强大,连方法的访问权限修饰符都可以声明为 private 的了,此时方法将不会成为你对外暴露的 API 的一部分,话说这个语法的改进不应该在 Java 8 中就施行的嘛~ 示例程序如下 :*
```java
package pers.huangyuhui.jdk9;

import org.junit.Test;

//注:其方法的默认权限为public哟
interface MyInterface {
    //jdk 8
    public default void defaultMethod() {
        privateMethod();
        System.out.println("this is default method");
    }

    //jdk 8
    public static void staticMethod() {
        System.out.println("this is static method");
    }

    //jdk 9
    private void privateMethod() {
        System.out.println("this is private method");
    }
}

/**
 * @project: jdk-learnig
 * @description: 测试JDK8及JDK9中的接口新语法
 * @author: 黄宇辉
 * @date: 8/21/2019-10:26 AM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public class Grammar implements MyInterface {

    //@Override
    //public void defaultMethod() {
    //    System.out.println("override the default method");
    //}

    @Test
    public void test() {
        MyInterface.staticMethod();
        new Grammar().defaultMethod();
    }
}
```

*程序运行结果如下所示 :*
```
this is static method
this is private method
this is default method
```


### 钻石操作符的使用升级
*与匿名实现类共同使用钻石操作符( diamond operator )在 Java 8 中如下的操作是会报错的,编译报错信息 : `Cannot use "<>" with anonymous inner classes`,在IDEA中将 Language level 调成 Java 9 即可解决编译错误啦*
```java
@Test
public void testComparator() {
    Comparator<Integer> comparator = new Comparator<>() {
        @Override
        public int compare(Integer o1, Integer o2) {
            return Integer.compare(o1, o2);
        }
    };
    System.out.println(comparator.compare(1, 2));
}

//简化代码:(JDK8)Lambda表达式写法
@Test
public void testComparatorWithLambda() {
    Comparator<Integer> comparator = (o1, o2) -> Integer.compare(o1, o2);
    System.out.println(comparator.compare(1, 2));
}

//简化代码:(JDK8)方法引用写法
@Test
public void testComparatorWithMethodRef() {
    Comparator<Integer> comparator = Integer::compare;
    System.out.println(comparator.compare(1, 2));
}
```


### 改进的 try-with-resourcs
*在 Java 7 中可以实现资源的自动关闭,但是`要求必须将需要自动关闭的所有资源必须在try子句中进行初始化`,否则编译不通过. 而在 Java 9 中可以将需要自动关闭的资源的实例放在 try 的小括号外哟,程序示例如下所示 :*
```java
//JDK7版本之前释放资源的写法
public void testTry() {
    InputStreamReader reader = null;
    try {
        reader = new InputStreamReader(System.in);
        char[] buffer = new char[50];
        int len;
        if ((len = reader.read(buffer)) != -1) {
            String s = new String(buffer, 0, len);
            System.out.println(s);
        }
    } catch (IOException e) {
        e.printStackTrace();
    } finally {
        if (reader != null) {
            try {
                reader.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}

//JDK7版本释放资源的写法
public void testTryWithJDK7() {
    try (InputStreamReader reader = new InputStreamReader(System.in)) {
        //reader = null; //注意:此时reader已被为设置为常量,既而不能修改
        char[] buffer = new char[50];
        int len;
        if ((len = reader.read(buffer)) != -1) {
            String s = new String(buffer, 0, len);
            System.out.println(s);
        }
    } catch (IOException e) {
        e.printStackTrace();
    }
}

//JDK9版本优化了JDK7释放资源的写法
public void testTryWithJDK9() {
    InputStreamReader reader = new InputStreamReader(System.in);
    OutputStreamWriter writer = new OutputStreamWriter(System.out);//unused
    try (reader; writer) {
        char[] buffer = new char[50];
        int len;
        if ((len = reader.read(buffer)) != -1) {
            String s = new String(buffer, 0, len);
            System.out.println(s);
        }
    } catch (IOException e) {
        e.printStackTrace();
    }
}
```


### String存储结构的变更
*从很多不同应用程序收集的信息表明,发现字符串是堆使用的主要组成部分. 而且,大多数字符串对象只包含一个字符,这样的字符只需要一个字节的存储空间,既而导致这些字符串对象的内部char数组中有一半的空间被闲置*

* JDK9 之前 String 底层使用 char 数组存储数据 `private final char value[]`
* JDK9 将 String 底层存储数据改为 byte 数组存储数据 `private final byte[] value`
* StringBuffer 和 StringBuilder 也同样做了变更,将以往 char 数组改为 byte 数组


### 快速创建只读集合
*JDK9在 List、Set 和 Map 集合中新增 of 静态方法,快速创建只读集合,程序示例如下所示 :*
```java
package pers.huangyuhui.jdk9;

import org.junit.Test;

import java.util.*;

/**
 * @project: jdk-learnig
 * @description: 快速创建只读集合
 * @author: 黄宇辉
 * @date: 8/21/2019-1:19 PM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public class CollectionTest {

    //在JDK9之前创建只读集合
    @Test
    public void test() {
        List<String> list = new ArrayList<>();
        list.add("A");
        list.add("B");
        list.add("C");
        //将List集合设为只读
        list = Collections.unmodifiableList(list);
        System.out.println(list);

        Set<String> set = new HashSet<>();
        set.add("E");
        set.add("F");
        set.add("G");
        //将Set集合设为只读
        set = Collections.unmodifiableSet(set);
        System.out.println(set);

        Map<String, String> map = new HashMap<>();
        map.put("k1", "v1");
        map.put("k2", "v2");
        map.put("k3", "v3");
        //将Map集合设为只读
        map = Collections.unmodifiableMap(map);
        System.out.println(map);
    }

    //在JDK9中,可以使用of方法直接快速创建只读集合
    @Test
    public void testWithJDK9() {
        //创建List只读集合
        List<String> list = List.of("A", "B", "C");
        System.out.println(list);
        //创建Set只读集合
        Set<String> set = Set.of("E", "F", "G");
        System.out.println(set);
        //创建List只读集合
        Map<String, String> map = Map.of("k1", "v1", "k2", "v2", "k3", "v3");
        System.out.println(map);
    }
}
```

*程序运行结果相同,如下所示 :*
```
[A, B, C]
[E, F, G]
{k1=v1, k2=v2, k3=v3}
```


### 增强的 InputStream API
*InputStream 终于有了一个非常有用的方法 : `transferTo`, 可以用来将数据直接传输到 OutputStream,这是在处理原始数据流时非常常见的一种用法,示例代码如下所示 :*
```java
@Test
public void testTransferTo() throws FileNotFoundException {
    ClassLoader classLoader = this.getClass().getClassLoader();
    InputStream is = classLoader.getResourceAsStream("from.txt");
    OutputStream os = new FileOutputStream("to.txt");
    try (is; os) {
        assert is != null;
        is.transferTo(os);
    } catch (IOException e) {
        e.printStackTrace();
    }
}
```


### 增强的 Stream API
*Java 的 Stream API 是 Java 标准款最好的改进之一,让开发者能够快速运算,从而能够有效的利用数据并行计算. Java 8 提供的Stream能够利用多核架构实现声明式的数据处理. 而在 Java 9 中,Stream API 变得更好,Stream 接口中添加了4个新方法 ( `takeWhile`, `dropWhile`, `ofNullable`, `iterate的重载方法` ), 及为你提供一个 Predicate( 判断条件 )来指定什么时候结束迭代. 除了对 Stream 本身的扩展, Optional 和 Stream 之间的结合也得到了改进,现在可以通过 Optional 的新对象 stream() 来将一个 Optional 对象转换为一个可能为空的 Stream 对象. 程序示例如下所示 :*
```java
public void testStream() {

    //takeWhile: 可以用于从Stream中获取一部分数据,接受一个Predicate来进行选择,在有序的Stream中,takeWhile返回从头开始的尽可能多的元素
    List<Integer> list = Arrays.asList(45, 43, 76, 87, 42, 77, 90, 73, 67, 88);
    list.stream().takeWhile((x) -> x < 80).forEach(System.out::println);
    //运行结果 : 45 43 76
    //从返回结果可以看出:takeWhile将会按照list集合有序的从45开始到第一个不符合条件为止的所有结果

    //dropWhile: dropWhile方法刚好与takeWhile想法,返回剩余的元素
    List<Integer> list2 = Arrays.asList(45, 43, 76, 87, 42, 77, 90, 73, 67, 88);
    list2.stream().dropWhile((x) -> x < 80).forEach(System.out::println);
    //运行结果 :87 42 77 90 73 67 88
    //从返回结果可以看出:dropWhile方法刚好和takeWhile方法形成互补,按照list集合有序的返回从第一个不满足条件元素开始到最后为止的所有结果


    //ofNullable: 在JDK8中Stream不能完全为null,否则会报空指针异常. 而在JDK9中ofNullable方法允许创建一个为空的Stream
    //Stream<Object> stream1 = Stream.of(null);
    //System.out.println(stream1.count());//NullPointerException
    //不报异常,允许这样写
    Stream<String> stringStream = Stream.of("AA", "BB", null);
    System.out.println(stringStream.count()); //程序运行结果:3
    //不报异常,允许这样写
    List<String> list3 = new ArrayList<>();
    list3.add("A");
    list3.add(null);
    System.out.println(list3.stream().count()); //程序运行结果:2
    //ofNullable():允许值为 null
    Stream<Object> stream = Stream.ofNullable(null);
    System.out.println(stream.count()); //程序运行结果:0
    Stream<String> stream2 = Stream.ofNullable("Hello World");
    System.out.println(stream2.count()); //程序运行结果:1

    //iterate()的重载方法
    //JDK8 使用iterate方法,需配合limit截止
    Stream.iterate(1, (x) -> x + 1).limit(10).forEach(System.out::print);
    //JDK9 使用iterate的重载方法可以直接使用Predicate来截止
    Stream.iterate(1, (x) -> x <= 10, (x) -> x + 1).forEach(System.out::print);
    //程序运行结果:12345678910

}
```


### 改进的 Optional 类
*Optional 类是在JDK8中新增的类,主要是为了解决空指针异常. 在JDK9中对这个类进行了改进,主要是新增了三个方法 : `stream`, `ifPresentOrElse` 和 `or`*

* `stream` : 将 Optional转为一个 Stream,如果 Optional 没有值就返回一个 Stream.empty
* `ifPresentOrElse` : 如果 Optional 包含值,则对其包含的值调用函数 action,即 action.accept(value),这与 ifPresent 一致.与 ifPresent 方法的区别在于 ifPresentOrElse 还有第二个参数 emptyAction. 如果 Optional 不包含值,那么 ifPresentOrElse 便会调用 emptyAction,即 emptyAction.run()
* `or` : 如果 Optional 有值,返回 Optional 指定的值,否则返回一个预设的值


### 全新的 HTTP 客服端 API
*JDK9 中有新的方式来处理 HTTP 调用. 它提供了一个新的HTTP客户端(HttpClient),它将替代仅适用于 blocking 模式的HttpURLConnection( HttpURLConnection是在HTTP 1.0的时代创建的,并使用了协议无关的方法 ),并提供对 WebSocket 和 HTTP/2 的支持. 此外,HTTP 客户端还提供 API 来处理 HTTP/2 的特性,比如流和服务器推送等功能. 全新的 HTTP 客户端 API 可以从 jdk.incubator.httpclient 模块中获取. 因为在默认情况下,这个模块是不能根据 classpath 获取的,需要使用`add modules`命令选项配置这个模块,将这个模块添加到 classpath中*


### javadoc 的 HTML5 支持
*JDK8 生成的 java 帮助文档是在 HTML4 中,而HTML4 已经是很久的标准了. JDK9 的 javadoc 现支持HTML5 标准*



*更多 JDK9 的新特性描述请参阅 : [What's New in JDK9](https://docs.oracle.com/javase/9/whatsnew/toc.htm)*
---
title: JDK 8新特性之Stream流的基本使用
date: 2019-08-18 21:09:29
tags: [JDK]
---

## 学习笔记 : JDK 8新特性之Stream流的基本使用

### 简介
*Java 8 API添加了一个新的抽象称为`Stream流`,其让你以一种声明的方式处理数据. Stream 使用一种类似用 SQL 语句从数据库查询数据的直观方式来提供一种对 Java 集合运算和表达的高阶抽象. 其 API 可以极大提高Java程序员的生产力,让程序员写出高效率、干净、简洁的代码. 其把将要处理的元素集合看作一种流, 流在管道中传输, 并且可以在管道的节点上进行处理, 比如筛选,排序,聚合等, 元素流在管道中经过中间操作 (intermediate operation) 的处理,最后由最终操作 (terminal operation) 得到前面处理的结果*

```
+--------------------+       +------+   +------+   +---+   +-------+
| stream of elements +-----> |filter+-> |sorted+-> |map+-> |collect|
+--------------------+       +------+   +------+   +---+   +-------+
```

*以上的流程可以转换Java代码 :*
```java
List<Integer> transactionsIds = 
widgets.stream()
        .filter(b -> b.getColor() == RED)
        .sorted((x,y) -> x.getWeight() - y.getWeight())
        .mapToInt(Widget::getWeight)
        .sum();
```


#### 特征
*Stream (流) 是一个来自数据源的元素队列并支持聚合操作 :*

* 元素是特定类型的对象,形成一个队列. Java中的`Stream并不会存储元素,而是按需计算`
* 数据源,流的来源可以是集合,数组,I/O channel,产生器generator等
* 聚合操作,类似SQL语句一样的操作,比如filter, map, reduce, find, match, sorted等


*和以前的Collection操作不同， Stream操作还有如下两个基础的特征 :*

* `Pipelining` : 中间操作都会返回流对象本身, 这样多个操作可以串联成一个管道,如同流式风格(fluent style), 这样做可以对操作进行优化,比如延迟执行(laziness)和短路(short-circuiting)
* `内部迭代` : 以前对集合遍历都是通过Iterator或者For-Each的方式, 显式的在集合外部进行迭代,这叫做外部迭代. Stream则提供了内部迭代的方式,通过`访问者模式(Visitor)`实现

*当使用一个流的时候,通常包括三个基本步骤 : 获取一个数据源(source) -> 数据转换 -> 执行操作获取想要的结果,每次转换原有 Stream 对象不改变,返回一个新的 Stream 对象(可以有多次转换),这就允许对其操作可以像链条一样排列,变成一个管道. `注 : Steam流属于管道流,既只能被消费/使用一次哟`*


#### 获取Stream流的方式
*`java.util.stream.Stream<T>`是Java8新加入的最常用的流接口 (注 : 这并不是一个函数式接口哟),获取一个流非常简单,有以下几种常用的方式 :*

1. *所有的`Collection`集合都可以通过stream默认方法获取流*
2. *通过`Stream`接口的静态方法`of`可以获取数组对应的流*


#### Stream流的常用方法
*Stream流模型的操作很丰富,其方法可以划分为两类 :*

1. *`延迟方法` : 返回值类型仍然是`Stream`接口自身类型的方法,因此支持链式调用( 除了终结方法外,其余方法均为延迟方法 )*
2. *`终结方法` : 返回值类型不再是`Stream`接口自身类型的方法,因此不再支持类似`StringBuilder`那样的链式调用,终结方法例如`count`和`forEach`*


#### 程序示例(对比)
*话不多说,来对比一下传统遍历并过滤集合与使用Stream流遍历并过滤集合的代码,感受下Stream流的美~*
```java
package pers.huangyuhui.Stream;

import org.junit.Test;

import java.util.ArrayList;
import java.util.List;

/**
 * @project: jdk-learnig
 * @description: 使用Stream流遍历并过滤集合
 * @author: 黄宇辉
 * @date: 8/18/2019-9:10 PM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public class StreamTest {

    //传统方式:遍历并过滤集合
    @Test
    public void test() {
        List<String> list = new ArrayList<>();
        list.add("a");
        list.add("bc");
        list.add("abc");
        list.add("bcd");
        list.add("abcd");
        //元素内容过滤
        List<String> list_a = new ArrayList<>();
        for (String s : list) {
            if (s.startsWith("a")) {
                list_a.add(s);
            }
        }
        //元素长度过滤
        List<String> list_len = new ArrayList<>();
        for (String s : list_a) {
            if (s.length() > 3) {
                list_len.add(s);
            }
        }
        //打印集合元素
        for (String s : list_len) {
            System.out.println(s); //结果:abcd
        }
    }

    //使用Steam流:遍历并过滤集合
    @Test
    public void testWithStream() {
        List<String> list = new ArrayList<>();
        list.add("a");
        list.add("bc");
        list.add("abc");
        list.add("bcd");
        list.add("abcd");
        //过滤集合元素后输出
        list.stream()
                .filter(name -> name.startsWith("a")) //元素内容过滤
                .filter(name -> name.length() > 3) //元素长度过滤
                //.forEach(name -> System.out.println(name));
                .forEach(System.out::println); //结果:abcd

    }

}
```


### 获取Stream流的两种方式
*获取流的两种方式 : 一将集合装换为Stream流, 二将数组转换为Stream流*
```java
package pers.huangyuhui.Stream;

import java.util.*;
import java.util.stream.Stream;

/**
 * @project: jdk-learnig
 * @description: 获取流的两种方式:将集合装换为Stream流,及将数组转换为Stream流
 * @author: 黄宇辉
 * @date: 8/19/2019-8:30 AM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public class GetStreamWay {

    //将List集合装换为Stream流
    public void testList() {
        List<String> list = new ArrayList<>();
        Stream<String> stream = list.stream();
    }

    //将Set集合转换为Stream流
    public void testSet() {
        Set<String> set = new HashSet<>();
        Stream<String> stream = set.stream();
    }

    //将Map集合转换为Stream流
    public void testMap() {
        Map<Integer, String> map = new HashMap<>();

        //分别获取并存储键与值
        Set<Integer> integers = map.keySet();
        Collection<String> values = map.values();
        //将存储键与值的集合转换为Stream流
        Stream<Integer> key = integers.stream();
        Stream<String> value = values.stream();

        //获取键值对(键与值映射关系),并将其转换为Stream流
        Set<Map.Entry<Integer, String>> entries = map.entrySet();
        Stream<Map.Entry<Integer, String>> entry = entries.stream();
    }

    //将数组转换为Stream流
    public void testArray() {
        String[] array = {"a", "ab", "bc", "abc", "bcd", "abcd"};
        Stream<String> stream = Stream.of(array);
    }

    // ······

}
```


### forEach方法
*该方法接收一个`Consumer`接口函数,会将每一个流元素交给该函数进行处理*
```java
void forEach(Consumer<? super T> action);
```

#### 复习Consumer接口
*此前已学过`java.util.function.Consumer<T>`函数式接口,该接口是一个消费型接口,其中唯一的抽象方法如下,意为消费一个指定泛型的数据*
```java
void accept(T t); 
```

#### 基本使用
*遍历输出数组中的元素*
```java
@Test
public void testForEach() {
    String[] array = {"a", "bc", "abc", "bcd", "abcd"};

    //第一种方式
    Stream<String> stream = Arrays.stream(array);
    stream.forEach(element -> System.out.println(element));

    //第二种方式
    Stream<String> stream2 = Stream.of(array);
    stream2.forEach(System.out::println);
}
```


### filter方法
*可以通过`filter`方法将一个流转换成另一个子集流,该接口接收一个`Predicate`函数式接口参数( 可以是一个Lambda或方法引用 )作为筛选条件*

#### 复习Predicate接口
*此前已学过`java.util.function.Predicate<T> `函数式接口,其中唯一的抽象方法如下,该方法将会产生一个boolean值结果,代表指定的条件是否满足,如果结果为true,那么Stream流的`filter`方法将会留用元素,反之舍弃*
```java
boolean test(T t);
```

#### 基本使用
*延迟方法与终结方法结合使用 : 过滤并遍历数组元素*
```java
@Test
public void testFilter() {
    String[] array = {"a", "bc", "abc", "bcd", "abcd"};
    Stream.of(array)
            .filter(element -> element.startsWith("a"))
            .filter(element -> element.length() > 3)
            .forEach(System.out::println); //abcd
}
```


### map方法
*如果需要将流中的元素映射到另一个流中,则可以使用`map`方法,方法签名如下,该接口需要一个函数式接口参数,可以将当前流中的T类型数据转换为另一种R类型的流*
```java
<R> Stream<R> map(function<? super T,? extends R> mapper);
```

#### 复习Function接口
*此前已学过`java.util.function.Function<T, R>`函数式接口,其中唯一的抽象方法如下,其可以将一种 T 类型转换为 R 类型,而这种转换的动作称为`映射`*
```java
R apply(T t);
```

#### 基本使用
*将String类型的数组元素装换为Integer类型并输出*
```java
//Lambda表达式写法
@Test
public void testMapWithLambda() {
    String[] array = {"1", "2", "3", "4", "5"};
    Stream<String> stream = Stream.of(array);
    Stream<Integer> stream2 = stream.map((String string) -> {
        return Integer.valueOf(string);
    });
    stream2.forEach(element -> System.out.println(element));
}

//Lambda表达式简写
@Test
public void testMapWithSimpleLambda() {
    String[] array = {"1", "2", "3", "4", "5"};
    Stream.of(array)
            .map(string -> Integer.valueOf(string))
            .forEach(element -> System.out.println(element));
}

//方法引用写法
@Test
public void testMapWithMethodRef() {
    String[] array = {"1", "2", "3", "4", "5"};
    Stream.of(array)
            .map(Integer::valueOf)
            .forEach(System.out::println);
}
```


### count方法
*正如就集合`Collection`当中的`size`方法一样,Stream流提供了`count`方法来计数元素个数,该方法返回一个long值代表元素个数(不再像同旧集合为int值). 注 : 该方法为终结方法哟~*
```java
long count();
```

#### 基本使用
*统计数组中以"a"开头的元素个数*
```java
@Test
public void testCount() {
    String[] array = {"a", "bc", "abc", "bcd", "abcd"};
    long count = Stream.of(array).filter(element -> element.startsWith("a")).count();
    System.out.println(count);
}
```


### limit方法
*`limit`方法可以对流进行截取,如果集合当前长度大于参数则进行截取,否则不进行操作*
```java
Stream<T> limit(long maxSize);
```

#### 基本使用
*截取数组中的前三个元素并输出*
```java
@Test
public void testLimit() {
    String[] array = {"a", "bc", "abc", "bcd", "abcd"};
    Stream.of(array).limit(3).forEach(System.out::println);
}
```


### skip方法
*如果希望跳过前几个元素,可以使用`skip`方法获取一个截取之后的流. 如果流的当前长度大于n,则跳过前n个,否则将会的到一个长度为0的空流*
```java
Stream<T> skip(long n);
```

#### 基本使用
*使用`skip`方法跳过数组中的前三个元素并输出*
```java
@Test
public void testSkip() {
    String[] array = {"a", "bc", "abc", "bcd", "abcd"};
    Stream.of(array).skip(3).forEach(System.out::println);
}
```


### concat方法
*如果有两个流,希望合并称为一个流,那么可以使用Stream接口的静态方法`concat`. 注 : 这是一个静态方法,不用与`java.lang.String`中的`concat`方法哟*
```java
Stream<T> skip(long n);
```

#### 基本使用
*通过合并两个数组中的元素得到新的流,并输出合并后的数组元素*
```java
//Lambda表达式写法
@Test
public void testConcat() {
    String[] array1 = {"a", "abc"};
    String[] array2 = {"abcd", "abcde"};
    Stream<String> stream1 = Stream.of(array1);
    Stream<String> stream2 = Stream.of(array2);
    Stream<Serializable> concatStream = Stream.concat(stream1, stream2);
    concatStream.forEach(System.out::println);
}

//简写
@Test
public void testSimpleConcat() {
    String[] array1 = {"a", "abc"};
    String[] array2 = {"abcd", "abcde"};
    Stream.concat(Stream.of(array1), Stream.of(array2)).forEach(System.out::println);
}
```



*参考《Java 8 Stream》 : https://www.runoob.com/java/java8-streams.html*
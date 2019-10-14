---
title: JDK 8新特性之Stream流的基本使用案例
date: 2019-08-19 13:27:27
tags: [JDK]
---

## 学习笔记 : JDK 8新特性之Stream流的基本使用案例

### 题目
*现有两个`ArrayList`集合分别存储着若干个元素,要求 : 一使用传统的for循环 / 增强for循环依次进行以下若干操作步骤, 二使用Stream流的方式依次进行以下若干操作步骤,相信最后你定会体会到`Stream`的美 (✪ω✪)~*

1. 第一个集合只要包含'E'字符的元素,并将其存储到一个新集合中
2. 第一个集合筛选之后只要前3个元素,并将其存储到新集合中
3. 第二个集合只要包含'K'字符的元素,并将其存储到新集合中
4. 第二个集合筛选之后不要前2两个元素,并将其存储到一个新集合中
5. 将两个集合合并成一个集合,并存储到一个新集合中
6. 根据合并后的集合中的元素值创建Element对象,并将其存储到新集合中
7. 打印整个集合的Element对象信息


### 解答
#### 传统方式的代码实现
```java
package pers.huangyuhui.jdk8.Stream;

import org.junit.Test;

import java.util.ArrayList;
import java.util.List;

//定义Element类
class Element {
    private String name;

    public Element(String name) {
        this.name = name;
    }

    @Override
    public String toString() {
        return "Element{" + "name='" + name + '\'' + '}';
    }
}

/**
 * @project: jdk-learnig
 * @description: 传统方式的代码实现
 * @author: 黄宇辉
 * @date: 8/19/2019-1:32 PM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public class Eaxmple {

    @Test
    public void testExample() {
        //A集合
        List<String> alist = new ArrayList<>();
        alist.add("ABC");
        alist.add("BCD");
        alist.add("CDE");
        alist.add("DEF");
        alist.add("EFG");
        alist.add("EEE");
        //1.第一个集合只要包含'E'字符的元素,并将其存储到一个新集合中
        List<String> alistA = new ArrayList<>();
        for (String s : alist) {
            if (s.contains("E")) {
                alistA.add(s);
            }
        }
        //2.第一个集合筛选之后只要前3个元素,并将其存储到新集合中
        List<String> alistB = new ArrayList<>();
        for (String s : alistA) {
            if (alistB.size() != 3) {
                alistB.add(s); //[CDE, DEF, EFG]
            }
        }

        //B集合
        List<String> blist = new ArrayList<>();
        blist.add("HIJ");
        blist.add("IJK");
        blist.add("JKL");
        blist.add("KLM");
        blist.add("KKK");
        //3.第二个集合只要包含'K'字符的元素,并将其存储到新集合中
        List<String> blistA = new ArrayList<>();
        for (String s : blist) {
            if (s.contains("K")) {
                blistA.add(s);
            }
        }
        //4.第二个集合筛选之后不要前2两个元素,并将其存储到一个新集合中
        List<String> blistB = new ArrayList<>();
        for (int i = 2; i < blistA.size(); i++) {
            blistB.add(blistA.get(i)); //[KLM, KKK]
        }

        //5.将两个集合合并成一个集合,并存储到一个新集合中
        List<String> abList = new ArrayList<>();
        abList.addAll(alistB);
        abList.addAll(blistB);

        //6.根据合并后的集合中的元素值创建Element对象,并将其存储到新集合中
        List<Element> pList = new ArrayList<>();
        for (String s : abList) {
            pList.add(new Element(s));
        }

        //7.打印整个集合的Element对象信息
        for (Element element : pList) {
            System.out.println(element.toString());
        }
    }

}
```

*程序运行结果如下 :*
```
Person{name='CDE'}
Person{name='DEF'}
Person{name='EFG'}
Person{name='KLM'}
Person{name='KKK'}
```


#### 使用Stream流的代码实现
```java
package pers.huangyuhui.jdk8.Stream;

import org.junit.Test;

import java.util.ArrayList;
import java.util.List;
import java.util.stream.Stream;

//定义Element类
class Element {
    private String value;

    public Element(String value) {
        this.value = value;
    }

    @Override
    public String toString() {
        return "Element{" + "value='" + value + '\'' + '}';
    }
}


/**
 * @project: jdk-learnig
 * @description: 使用Stream流的代码实现
 * @author: 黄宇辉
 * @date: 8/19/2019-3:57 PM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public class Example {

    @Test
    public void testWithStream() {
        //A集合
        List<String> alist = new ArrayList<>();
        alist.add("ABC");
        alist.add("BCD");
        alist.add("CDE");
        alist.add("DEF");
        alist.add("EFG");
        alist.add("EEE");

        //1.第一个集合只要包含'E'字符的元素,并将其存储到一个新集合中
        //2.第一个集合筛选之后只要前3个元素,并将其存储到新集合中
        Stream<String> streamA = alist.stream()
                .filter(value -> value.contains("E"))
                .limit(3);


        //B集合
        List<String> blist = new ArrayList<>();
        blist.add("HIJ");
        blist.add("IJK");
        blist.add("JKL");
        blist.add("KLM");
        blist.add("KKK");

        //3.第二个集合只要包含'K'字符的元素,并将其存储到新集合中
        //4.第二个集合筛选之后不要前2两个元素,并将其存储到一个新集合中
        Stream<String> streamB = blist.stream()
                .filter(value -> value.contains("K"))
                .skip(2);

        //5.将两个集合合并成一个集合,并存储到一个新集合中
        //6.根据合并后的集合中的元素值创建Element对象,并将其存储到新集合中
        //7.打印整个集合的Element对象信息
        Stream.concat(streamA, streamB)
                .map(Element::new) //.map(value -> new Element(value))
                .forEach(System.out::println);

    }
}
```

*程序运行结果如下 :*
```
Element{value='CDE'}
Element{value='DEF'}
Element{value='EFG'}
Element{value='KLM'}
Element{value='KKK'}
```
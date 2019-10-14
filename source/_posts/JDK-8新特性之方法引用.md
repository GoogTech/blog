---
title: JDK 8新特性之方法引用
date: 2019-08-18 10:42:23
tags: [JDK]
---

## 学习笔记 : JDK 8新特性之方法引用

### 简介
*当要传递给Lambda体的操作已经有实现的方法时,就可以使用`方法引用`咯~ 方法引用可以看做是Lambda表达式深层的表达,换句话说 : 方法引用就是Lambda表达式,既函数式接口的一个实例,通过方法的名字来指定一个方法,可以认为是Lambda表达式的一个语法糖~*

#### 要求
*实现接口的抽象方法的参数列表和返回值类型,必须与方法引用的方法的参数列表和返回值类型保持一致*

#### 格式
*使用操作符 `::` 将类(或对象)与方法名分隔开来*

#### 语法
* 对象 :: 实例方法名
* 类 :: 静态方法名
* 类 :: 实例方法名



### 基本使用
*下面通过一个简单的程序示例,来对比Lambda表达式与方法引用写法之间的区别 :*
```java
package pers.huangyuhui.method;

import org.junit.Ignore;
import org.junit.Test;

//定义一个函数式接口
@FunctionalInterface
interface Printable {
    void print(String s);
}

/**
 * @project: jdk-learnig
 * @description: Java 8新特性之方法引用的基本使用
 * @author: 黄宇辉
 * @date: 8/18/2019-2:15 PM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public class methodTest {

    //定义打印字符的方法
    public void printResult(String s, Printable printable) {
        printable.print(s);
    }

    //传统写法
    @Test
    public void testPrintString() {
        printResult("hello lambda", new Printable() {
            @Override
            public void print(String s) {
                System.out.println(s);
            }
        });
    }

    //Lambda表达式写法
    @Test
    public void testPrintStringWithLambda() {
        printResult("hello lambda", s1 -> System.out.println(s1));
    }

    //方法引用写法
    @Test
    public void testPrintStringWithMethodRef() {
        //对象 :: 实例方法名
        printResult("hello lambda", System.out::println);
    }
}
```

#### 语义分析
*例如上例中,`System.out`对象中有一个重载的`println(String)`方法恰好就是我们所需要的,那么对于`printResult`方法的函数式接口参数,对比以下两种写法,完全等效 :*

* Lambda表达式写法 : `s -> System.out.println(s)`
* 方法引用写法 : `System.out::println`

*第一种语义是指 : 拿到参数之后,继而传递给`System.out.println`方法去处理*
*第二种语义是指 : 直接让`System.out`中的`println`方法取代Lambda. 这种写法复用了已有方案,更加简洁*



### 常用的几种方法引用

#### 通过对象名引用成员方法
```java
package pers.huangyuhui.method;

import org.junit.Test;

//定义函数式接口
@FunctionalInterface
interface Printable {
    void print(String s);
}

//定义含有成员方法类
class Method {
    public void printUpperCaseStr(String s) {
        System.out.println(s.toUpperCase());
    }
}

/**
 * @project: jdk-learnig
 * @description: 通过对象名引用成员方法
 * @author: 黄宇辉
 * @date: 8/18/2019-3:07 PM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public class RefMethodByObjTest {

    public void printResult(String s, Printable printable) {
        printable.print(s);
    }

    //传统方法
    @Test
    public void testPrintResult() {
        printResult("hello lambda", new Printable() {
            @Override
            public void print(String s) {
                Method obj = new Method(); //创建Method对象
                obj.printUpperCaseStr(s); //调用对象的成员方法,将字符串转换为大写后输出
            }
        });
    }

    //Lambda表达式写法
    @Test
    public void testPrintResultWithLambda() {
        printResult("hello lambda", s -> {
            Method obj = new Method();
            obj.printUpperCaseStr(s);
        });
    }

    //方法引用写法
    @Test
    public void testPrintResultWithMethodRef() {
        Method obj = new Method();
        printResult("hello lambda", obj::printUpperCaseStr);
    }
}
```

#### 通过类名引用静态方法
```java
package pers.huangyuhui.method;

import org.junit.Test;

//定义函数式接口
@FunctionalInterface
interface calculable {
    int add(int x, int y);
}

//定义含有静态方法类
class Method {
    public static int add(int x, int y) {
        return x + y;
    }
}


/**
 * @project: jdk-learnig
 * @description: 通过类名引用静态方法
 * @author: 黄宇辉
 * @date: 8/18/2019-3:36 PM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public class RefStatisMethodByClazzName {

    public int getResult(int x, int y, calculable calculable) {
        return calculable.add(x, y);
    }

    //传统写法
    @Test
    public void testGetResult() {
        int result = getResult(1, 2, new calculable() {
            @Override
            public int add(int x, int y) {
                return Method.add(x, y);
            }
        });
        System.out.println(result);
    }

    //Lambda表达式写法
    @Test
    public void testGetResultWithLambda() {
        int result = getResult(1, 2, (x, y) -> Method.add(x, y));
        System.out.println(result);
    }

    //方法引用写法
    @Test
    public void testGetResultWithMethodRef() {
        int result = getResult(1, 2, Method::add);
        System.out.println(result);
    }
}
```

#### 通过Supper引用父类中的成员方法
```java
package pers.huangyuhui.method;

import org.junit.Test;

//定义一个函数式接口
@FunctionalInterface
interface Agreeable {
    void greet();
}

//定义一个父类
class Human {
    public void hello() {
        System.out.println("hello my sun !");
    }
}

/**
 * @project: jdk-learnig
 * @description: 通过Supper引用父类中的成员方法
 * @author: 黄宇辉
 * @date: 8/18/2019-4:06 PM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public class RefMethodBySupper extends Human {

    @Override
    public void hello() {
        System.out.println("hello my father !");
    }

    public void printResult(Agreeable agreeable) {
        agreeable.greet();
    }

    //传统写法
    @Test
    public void testPrintResult() {
        printResult(new Agreeable() {
            @Override
            public void greet() {
                RefMethodBySupper.super.hello();
            }
        });
    }

    //Lambda表达式写法
    @Test
    public void testPrintResultWithLambda() {
        printResult(() -> super.hello());
    }

    //方法引用写法
    @Test
    public void testPrintResultWithMethodRef() {
        printResult(super::hello);
    }

}
```

#### 通过this引用成员方法
```java
package pers.huangyuhui.method;

import org.junit.Test;

//定义函数式接口
@FunctionalInterface
interface Supermarket {
    void buy();
}

/**
 * @project: jdk-learnig
 * @description: 通过this引用成员方法
 * @author: 黄宇辉
 * @date: 8/18/2019-4:30 PM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public class RefMethodByThis {

    public void buyLaptop() {
        System.out.println("buy a laptop successfully");
    }

    public void printResult(Supermarket supermarket) {
        supermarket.buy();
    }

    //传统写法
    @Test
    public void testPrintResult() {
        printResult(new Supermarket() {
            @Override
            public void buy() {
                buyLaptop();
            }
        });
    }

    //Lambda表达式写法
    @Test
    public void testPrintResultWithLambda() {
        printResult(() -> this.buyLaptop());
    }

    //方法引用写法
    @Test
    public void testPrintResultWithMethodRef() {
        printResult(this::buyLaptop);
    }

}
```

#### 类的构造器引用
```java
package pers.huangyuhui.method;

import org.junit.Test;

//定义函数式接口
@FunctionalInterface
interface UserBuilder {
    User builder(String name);
}

//定义含待引用的构造方法类
class User {
    private String name;

    public User(String name) {
        this.name = name;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

}

/**
 * @project: jdk-learnig
 * @description: 类的构造器引用
 * @author: 黄宇辉
 * @date: 8/18/2019-7:09 PM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public class RefConstructor {

    public void printResult(String name, UserBuilder userBuilder) {
        User user = userBuilder.builder(name);
        System.out.println(user.getName());
    }

    //传统写法
    @Test
    public void testPrintResult() {
        printResult("yubuntu0109", new UserBuilder() {
            @Override
            public User builder(String name) {
                return new User(name);
            }
        });
    }

    //Lambda表达式写法
    @Test
    public void testPrintResultWithLambda() {
        printResult("yubuntu0109", s -> new User(s));
    }

    //方法引用写法
    @Test
    public void testPrintResultWithMethodRef() {
        printResult("yubuntu0109", User::new);
    }

}
```

#### 数组的构造器引用
```java
package pers.huangyuhui.method;

import org.junit.Test;

import java.util.Arrays;

//定义函数式接口
@FunctionalInterface
interface ArrayBuilder {
    int[] builderArray(int length);
}

/**
 * @project: jdk-learnig
 * @description: 数组的构造器引用
 * @author: 黄宇辉
 * @date: 8/18/2019-7:51 PM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public class RefArrayConstructor {

    public static int[] getArray(int length, ArrayBuilder arrayBuilder) {
        return arrayBuilder.builderArray(length);
    }

    //传统写法
    @Test
    public void testgetArray() {
        int[] array = getArray(10, new ArrayBuilder() {
            @Override
            public int[] builderArray(int length) {
                return new int[length];
            }
        });
        System.out.println(Arrays.toString(array));
    }

    //Lambda表达式写法
    @Test
    public void testArrayWithLambda() {
        int[] array = getArray(10, length -> new int[length]);
        System.out.println(Arrays.toString(array));
    }

    //方法引用写法
    @Test
    public void testArrayWithMethodRef() {
        int[] array = getArray(10, int[]::new);
        System.out.println(Arrays.toString(array));
    }
}
```
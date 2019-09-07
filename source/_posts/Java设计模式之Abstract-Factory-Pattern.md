---
title: Java设计模式之Abstract Factory Pattern
date: 2019-09-07 21:56:25
tags: [Java,design and pattern]
---

## 学习笔记 : Java设计模式之Abstract Factory Pattern

### 概述
*`工厂方法模式`通过引入工厂等级结构解决了`简单工厂模式`中工厂类职责太重的问题,但由于工厂方法模式中每个具体工厂只有一个或一组重载的工厂方法,既只能生产一种产品,可能会导致系统中存在大量的工厂类,势必会增加系统的开销. 抽象工厂模式是所有形式的工厂模式中最为抽象和最具一般性的一种形式. 抽象工厂模式与工厂方法模式的最大区别在于 : 工厂方法模式针对的是一个`产品等级结构`,而抽象工厂模式需要面对多个产品等级结构,一个工厂等级结构可以负责多个不同产品等级结构中的产品对象的创建. 当一个工厂等级结构可以创建出分属于不同产品等级结构的一个产品族中的所有对象时,抽象工厂模式比工厂方法模式更加简单,更有效率~ 抽象工厂模式的定义为 : 提供一个创建一系列相关或相互依赖对象的接口,而无须指定它们具体的类( Abstract Factory Pattern : Provide an interface for creating families of related or dependent objects without sprcifying their concrete classes. )*


### 抽象工厂模式中的角色
1. *`AbstractFactory( 抽象工厂 )` : 它声明了一组用于创建一族产品的方法,每一个方法对应一种产品.*
2. *`ConcreteFactory( 具体工厂 )` : 它实现了在抽象工厂中声明的创建产品的方法,生成一组具体产品,这些产品构成了一个`产品族`,每一个产品都位于某个产品等级结构中*
3. *`AbstractProduct( 抽象产品 )` : 它为每种产品声明接口,在抽象产品中声明了产品所具有的业务方法.*
4. *`ConcreteProduct( 具体产品 )` : 它定义具体工厂生产的具体产品对象,实现抽象产品接口中声明的业务方法.*


### 示例程序
*下面是一个使用了抽象工厂模式的简单示例程序,其程序功能( 模拟 )描述为 : 开发一套基于 Java 桌面软件的界面皮肤库,用户可以自由切换界面皮肤,例如有 Spring 与 Summer 风格等皮肤~*

1. *Button接口 : 接口按钮, 充当抽象产品*
```java
package pers.huangyuhui.abstract_factory_pattern.abstract_factory;

/**
 * @project: design-patterns
 * @description: 接口按钮, 充当抽象产品
 * @author: 黄宇辉
 * @date: 9/7/2019-5:18 PM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public interface Button {
    void display();
}
```

2. *SpringButton类 : Spring风格皮肤的按钮类, 充当具体产品*
```java
package pers.huangyuhui.abstract_factory_pattern.abstract_factory;

/**
 * @project: design-patterns
 * @description: Spring风格皮肤的按钮类, 充当具体产品
 * @author: 黄宇辉
 * @date: 9/7/2019-5:19 PM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public class SpringButton implements Button {
    @Override
    public void display() {
        System.out.println("spring theme———>this is button");
    }
}
```

3. *SummerButton类 : Summer风格皮肤的按钮类, 充当具体产品*
```java
package pers.huangyuhui.abstract_factory_pattern.abstract_factory;

/**
 * @project: design-patterns
 * @description: Summer风格皮肤的按钮类, 充当具体产品
 * @author: 黄宇辉
 * @date: 9/7/2019-5:20 PM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public class SummerButton implements Button {

    @Override
    public void display() {
        System.out.println("Summer theme———>button");
    }
}
```

4. *TextField接口 : 文本框接口, 充当抽象产品*
```java
package pers.huangyuhui.abstract_factory_pattern.abstract_factory;

/**
 * @project: design-patterns
 * @description: 文本框接口, 充当抽象产品
 * @author: 黄宇辉
 * @date: 9/7/2019-5:22 PM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public interface TextField {
    void display();
}
```

5. *SpringTextField类 : Spring风格皮肤的文本框类, 充当具体产品*
```java
package pers.huangyuhui.abstract_factory_pattern.abstract_factory;

/**
 * @project: design-patterns
 * @description: Spring风格皮肤的文本框类, 充当具体产品
 * @author: 黄宇辉
 * @date: 9/7/2019-5:22 PM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public class SpringTextField implements TextField {

    @Override
    public void display() {
        System.out.println("Spring theme———>this is text field");
    }
}
```

6. *SummerTextField类 : Summer风格皮肤的文本框类, 充当具体产品*
```java
package pers.huangyuhui.abstract_factory_pattern.abstract_factory;

/**
 * @project: design-patterns
 * @description: Summer风格皮肤的文本框类, 充当具体产品
 * @author: 黄宇辉
 * @date: 9/7/2019-5:23 PM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public class SummerTextField implements TextField {

    @Override
    public void display() {
        System.out.println("Summer theme———>this is text field");
    }
}
```

7. *ComboBob类 : 组合框接口, 充当抽象产品*
```java
package pers.huangyuhui.abstract_factory_pattern.abstract_factory;

/**
 * @project: design-patterns
 * @description: 组合框接口, 充当抽象产品
 * @author: 黄宇辉
 * @date: 9/7/2019-5:24 PM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public interface ComboBox {
    void display();
}
```

8. *SpringComboBox类 : Spring风格皮肤的组合框类, 充当具体产品*
```java
package pers.huangyuhui.abstract_factory_pattern.abstract_factory;

/**
 * @project: design-patterns
 * @description: Spring风格皮肤的组合框类, 充当具体产品
 * @author: 黄宇辉
 * @date: 9/7/2019-5:25 PM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public class SpringComboBox implements ComboBox {
    @Override
    public void display() {
        System.out.println("Spring theme———>this is comboxBox");
    }
}
```

9. *SummerComboBox类 : Summer风格皮肤的组合框类, 充当具体产品*
```java
package pers.huangyuhui.abstract_factory_pattern.abstract_factory;

/**
 * @project: design-patterns
 * @description: Summer风格皮肤的组合框类, 充当具体产品
 * @author: 黄宇辉
 * @date: 9/7/2019-5:25 PM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public class SummerComboBox implements ComboBox {
    @Override
    public void display() {
        System.out.println("Summer theme———>this is comboBox");
    }
}
```

10. *SkinFactory接口 : 界面皮肤工厂接口, 充当抽象工厂*
```java
package pers.huangyuhui.abstract_factory_pattern.abstract_factory;

/**
 * @project: design-patterns
 * @description: 界面皮肤工厂接口, 充当抽象工厂
 * @author: 黄宇辉
 * @date: 9/7/2019-5:26 PM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public interface SkinFactory {
    Button createButton();

    TextField createTextField();

    ComboBox createComboBox();
}
```

11. *SpringSkinFactory类 : Spring皮肤风格的工厂类, 充当具体工厂*
```java
package pers.huangyuhui.abstract_factory_pattern.abstract_factory;

/**
 * @project: design-patterns
 * @description: Spring皮肤风格的工厂类, 充当具体工厂
 * @author: 黄宇辉
 * @date: 9/7/2019-5:29 PM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public class SpringSkinFactory implements SkinFactory {
    @Override
    public Button createButton() {
        return new SpringButton();
    }

    @Override
    public TextField createTextField() {
        return new SpringTextField();
    }

    @Override
    public ComboBox createComboBox() {
        return new SpringComboBox();
    }
}
```

12. *SummerSkinFactory类 : Summer风格皮肤的工厂类, 充当具体工厂*
```java
package pers.huangyuhui.abstract_factory_pattern.abstract_factory;

/**
 * @project: design-patterns
 * @description: Summer风格皮肤的工厂类, 充当具体工厂
 * @author: 黄宇辉
 * @date: 9/7/2019-5:33 PM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public class SummerSkinFactory implements SkinFactory {
    @Override
    public Button createButton() {
        return new SummerButton();
    }

    @Override
    public TextField createTextField() {
        return new SummerTextField();
    }

    @Override
    public ComboBox createComboBox() {
        return new SummerComboBox();
    }
}
```

13. *Client类 : 客户端测试类*
```java
package pers.huangyuhui.abstract_factory_pattern.abstract_factory;

/**
 * @project: design-patterns
 * @description: 客户端测试类
 * @author: 黄宇辉
 * @date: 9/7/2019-5:30 PM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public class Client {

    public static void main(String[] args) {
        SkinFactory skinFactory = new SpringSkinFactory();
        skinFactory.createButton().display();
        skinFactory.createTextField().display();
        skinFactory.createComboBox().display();

        System.out.println("———————————————————");

        SkinFactory skinFactory2 = new SummerSkinFactory();
        skinFactory2.createButton().display();
        skinFactory2.createTextField().display();
        skinFactory2.createComboBox().display();

    }
}
```

14. *程序运行结果如下所示 :*
```
spring theme———>this is button
Spring theme———>this is text field
Spring theme———>this is comboxBox
———————————————————
Summer theme———>button
Summer theme———>this is text field
Summer theme———>this is comboBox
```

#### 示例程序的类图
![](Java设计模式之Abstract-Factory-Pattern/Abstract-Factory-Pattern-ClassDiagram.png)
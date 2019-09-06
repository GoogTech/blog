---
title: Java设计模式之Simple Factory Pattern
date: 2019-09-06 09:35:53
tags: [Java,design and pattern]
---

## 学习笔记 : Java设计模式之Simple Factory Pattern

### 概述
*简单工厂模式( Simple Factory Pattern) : 定义一个工厂类,他可以根据参数的不同返回不同类的实例,被创建的实例通常都具有共同的父类. 其是一种创建型模式,需注意 : 简单工厂模式并不属于23种经典设计模式,但通常将它作为学习其他工厂模式的基础.*

### 简单工厂模式中的角色
1. *`Factory( 工厂角色 )` : 工厂角色既工厂类,它是简单工厂模式的核心,负责实现创建所有产品实例的内部结构. 工厂类可以被外界访问直接调用,创建所需的产品对象. 在工厂类中提供了静态的工厂方法,其返回类型为抽象产品类型.*
2. *`Product( 抽象产品角色 )` : 它是工厂类创建的所有对象的父类,封装了各种对象的公有方法,它的引入提高了系统的灵活性.*
3. *`ConcreteProduct( 具体产品角色 )` : 它是简单工厂模式的创建目标,所有被创建的对象都充当这个角色的某个具体类的实例. 每一个具体产品角色都继承了抽象产品角色,需要实现在抽象产品中的抽象方法.*

### 程序示例
*下面是一个使用简单工厂模式的简单程序示例,其功能( 模拟 )描述 : 通过设置不同的参数即可得到不同类型的图表,而且可以较为方便地对图表进行扩展,以便能够在将来增加一些新类型的图表.*

1. *Chart接口 : 抽象图表接口, 充当抽象产品类*
```java
package pers.huangyuhui.simple_factory_pattern;

/**
 * @project: design-patterns
 * @description: 抽象图表接口, 充当抽象产品类
 * @author: 黄宇辉
 * @date: 9/6/2019-9:36 AM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public interface Chart {

    void display();
}
```

2. *HistogramChart类 : 柱状图类, 充当具体产品类*
```java
package pers.huangyuhui.simple_factory_pattern;

/**
 * @project: design-patterns
 * @description: 柱状图类, 充当具体产品类
 * @author: 黄宇辉
 * @date: 9/6/2019-9:37 AM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public class HistogramChart implements Chart {

    public HistogramChart() {
        System.out.println("创建柱状图");
    }

    @Override
    public void display() {
        System.out.println("显示柱状图");
    }
}
```

3. *PieChart类 : 饼状图类, 充当具体产品类*
```java
package pers.huangyuhui.simple_factory_pattern;

/**
 * @project: design-patterns
 * @description: 饼状图类, 充当具体产品类
 * @author: 黄宇辉
 * @date: 9/6/2019-9:38 AM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public class PieChart implements Chart {

    public PieChart() {
        System.out.println("创建饼状图");
    }

    @Override
    public void display() {
        System.out.println("显示饼状图");
    }
}
```

4. *ChartFactory类 : 图表工厂类, 充当工厂类*
```java
package pers.huangyuhui.simple_factory_pattern;

/**
 * @project: design-patterns
 * @description: 图表工厂类, 充当工厂类
 * @author: 黄宇辉
 * @date: 9/6/2019-9:40 AM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public class ChartFactory {

    //静态工厂方法
    public static Chart getChar(String type) {
        Chart chart = null;
        if (type.equalsIgnoreCase("histogram")) {
            chart = new HistogramChart();
            System.out.println("初始化柱状图");
        } else if (type.equalsIgnoreCase("pie")) {
            chart = new PieChart();
            System.out.println("初始化饼状图");
        } else {
            System.out.println("error : not found the object for the specified parameter");
        }
        return chart;
    }
}
```

5. *Client类 : 客户端测试类*
```java
package pers.huangyuhui.simple_factory_pattern;

/**
 * @project: design-patterns
 * @description: 客户端测试类
 * @author: 黄宇辉
 * @date: 9/6/2019-9:47 AM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public class Client {
    public static void main(String[] args) {
        Chart histogram = ChartFactory.getChar("histogram");
        histogram.display();
    }
}
```

5. *程序运行结果如下所示 :*
```
创建柱状图
初始化柱状图
显示柱状图
```

#### 示例程序类图
![](Java设计模式之Simple-Factory-Pattern/Simple-Factory-Pattern-ClassDiagram.png)
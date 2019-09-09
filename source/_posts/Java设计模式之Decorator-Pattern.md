---
title: Java设计模式之Decorator Pattern
date: 2019-09-08 21:47:30
tags: [Java,design and pattern]
---

## 学习笔记 : Java设计模式之Decorator Pattern

### 概述
*装饰设计模式( 对象结构型模式 )是一种用于替代继承的技术,它通过一种无须定义子类的方式来给对象动态增加职责,使用对象之间的关联关系取代类之间的继承关系. 装饰设计模式的定位为 : 动态地给一个对象增加一些额外的职责. 就扩展而言,装饰模式提供了一种比使用子类更加灵活的替代方案. ( Decorator Pattern : Attach additonal responsibilities to an object dynamically. Decorators provide a flexible alternative to subclassing for extending functionality )*


### 装饰设计模式中的角色
1. *`Component ( 抽象构件 )` : 它是`具体构件`和`抽象装饰类`的共同父类,声明了在具体构件中实现的业务方法,它的引入可以使客户端以一致的方式处理未被装饰的对象及装饰之后的对象,实现客户端的透明操作.*
2. *`ConcreteComponent ( 具体构件 )` : 它是`抽象构件类`的子类,用于定义具体的构件对象,实现了在抽象构件中声明的方法,装饰类可以给它增加额外的职责( 方法 )*
3. *`Decorator ( 抽象装饰类 )` : 它也是`抽象构件类`的子类,用于给`具体构件`增加职责,但是具体职责在其子类中实现. 它维护一个指向抽象构件的引用,通过该引用可以调用装饰之前构件对象的方法,并通过子类扩展该方法,继而达到装饰的目的.*
4. *`ConcreteDecorator ( 具体装饰类 )` : 它是`抽象装饰类`的子类,负责向构件添加新的职责. 每一个具体装饰类都定义了一些新的行为,它可以调用在抽象装饰类中定义的方法,并可以增加新的方法用于扩展对象的行为.*


### 示例程序-透明装饰模式
*下面是一个使用了装饰设计模式的简单示例程序,其示例说明为 : 使用面向对象技术开发了一套图形界面构件库,该构件库提供了大量的基本结构,如窗体,文本框,列表框等,由于在使用时用户经常要求定制一些特殊的显示效果,如带滚动条的窗体,或带黑色边框的文本框等. 因此经常需要对该构件库进行扩展以增强其功能,既而可以使用装饰设计模式来设计该界面构件库哟~*

1. *Component接口 : 抽象界面构件类, 充当抽象构件类*
```java
package pers.huangyuhui.decorator_pattern;

/**
 * @project: design-patterns
 * @description: 抽象界面构件类, 充当抽象构件类
 * @author: 黄宇辉
 * @date: 9/9/2019-2:47 PM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public abstract class Component {

    public abstract void display();
}
```

2. *Window类 : 窗体类, 充当具体构件类*
```java
package pers.huangyuhui.decorator_pattern;

/**
 * @project: design-patterns
 * @description: 窗体类, 充当具体构件类
 * @author: 黄宇辉
 * @date: 9/9/2019-2:48 PM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public class Window extends Component {

    @Override
    public void display() {
        System.out.println("显示窗体 !");
    }
}
```

3. *TextBox类 : 文本框类, 充当具体构件类*
```java
package pers.huangyuhui.decorator_pattern;

/**
 * @project: design-patterns
 * @description: 文本框类, 充当具体构件类
 * @author: 黄宇辉
 * @date: 9/9/2019-2:50 PM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public class TextBox extends Component {
    @Override
    public void display() {
        System.out.println("显示文本框 !");
    }
}
```

4. *ListBox类 : 列表框类, 充当具体构件类*
```java
package pers.huangyuhui.decorator_pattern;

/**
 * @project: design-patterns
 * @description: 列表框类, 充当具体构件类
 * @author: 黄宇辉
 * @date: 9/9/2019-2:51 PM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public class ListBox extends Component {

    @Override
    public void display() {
        System.out.println("显示列表框 !");
    }
}
```

5. *ComponentDecorator类 : 构件装饰类, 充当抽象装饰类*
```java
package pers.huangyuhui.decorator_pattern;

/**
 * @project: design-patterns
 * @description: 构件装饰类, 充当抽象装饰类
 * @author: 黄宇辉
 * @date: 9/9/2019-2:52 PM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public class ComponentDecorator extends Component {

    //维持对抽象构件类型对象的引用
    private Component component;

    //注入抽象构件类型的对象
    public ComponentDecorator(Component component) {
        this.component = component;
    }

    @Override
    public void display() {
        component.display();
    }
}
```

6. *ScrollBarDecorator类 : 滚动条装饰类, 充当具体装饰类*
```java
package pers.huangyuhui.decorator_pattern;

/**
 * @project: design-patterns
 * @description: 滚动条装饰类, 充当具体装饰类
 * @author: 黄宇辉
 * @date: 9/9/2019-2:54 PM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public class ScrollBarDecorator extends ComponentDecorator {

    public ScrollBarDecorator(Component component) {
        super(component);
    }

    @Override
    public void display() {
        this.setScrollBar();
        super.display();
    }

    private void setScrollBar() {
        System.out.println("为构件添加滚动条 !");
    }
}
```

7. *BlackBorderDecorator类 : 黑色边框装饰类, 充当具体装饰类*
```java
package pers.huangyuhui.decorator_pattern;

/**
 * @project: design-patterns
 * @description: 黑色边框装饰类, 充当具体装饰类
 * @author: 黄宇辉
 * @date: 9/9/2019-2:59 PM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public class BlackBorderDecorator extends ComponentDecorator {

    public BlackBorderDecorator(Component component) {
        super(component);
    }

    @Override
    public void display() {
        this.setBlackBorder();
        super.display();
    }

    private void setBlackBorder() {
        System.out.println("为构件添加黑色边框 !");
    }
}
```

8. *Client类 : 客户端测试类*
```java
package pers.huangyuhui.decorator_pattern;

/**
 * @project: design-patterns
 * @description: 客户端测试类
 * @author: 黄宇辉
 * @date: 9/9/2019-3:03 PM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public class Client {

    public static void main(String[] args) {
        Component component = new Window();
        component.display();

        System.out.println("------------------");

        Component component2 = new ScrollBarDecorator(component);
        component2.display();
    }
}
```

#### 示例程序的类图
![](Java设计模式之Decorator-Pattern/Decorator-Pattern-ClassDiagram.png)



### 示例程序-半透明装饰模式
*暂略 · · ·*
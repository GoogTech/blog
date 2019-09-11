---
title: Java继承与委托中的一致性
date: 2019-09-09 15:17:00
tags: [Java,design and pattern]
---

## 学习笔记 : `Decorator Pattern`的延伸阅读-继承与委托中的一致性

### 继承-父类和子类的一致性
*父类和子类具有一致性,示例代码如下所示 :*
```java
class Parent {
    ···
    void parentMethod(){
        ···
    }
}

class Child extends Parent {
    ···
    void childMethod(){
        ···
    }
}
```

*此时,Child类的实例可以被保存在 Parent 类型中的变量中,所以可以调用从 Parent 类中继承的方法,也就是说,可以像操作 Parent 类的实例一样操作 Child 类的实例. 如下是将子类当做父类看待的示例代码~*
```java
Parent obj = new Child();
obj.parentMethod();
```

*相反,如果想将父类当做子类一样操作,则需要先进行类型转换哟~*
```java
Parent obj = new Child();
((Child)obj).childMethod();
```


### 委托-自己和被委托对象的一致性
*使用委托让接口具有透明性,自己和被委托对象具有一致性 : 如下示例程序, Rose 和 Violet 都有相同的 method 方法, Rose 将 method 方法的处理委托给了 Violet*
```java
/* or interface */abstract class Flower {
    abstract void method();
}

class Rose extends Flower {
    Violet obj = ···
    void method() {
        obj.method();
    }
}

class Violet extends Flower {
    void method() {
        ···
    }
}
```

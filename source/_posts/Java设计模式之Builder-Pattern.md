---
title: Java设计模式之Builder Pattern
date: 2019-09-17 22:00:54
tags: [java,design and pattern]
---

## 学习笔记 : Java设计模式之Builder Pattern

### 概述
*缔造者设计模式 : 将一个复杂对象的构建与它的表示分离,使得同样的构建过程可以创建不同的表示( Builder Pattern : Separate the construction of complex object from its representation so that the same construction process can create different representations )*

* 扩 : 建造者模式是一种对象创建型模式,它将客户端与包含多个部件的复杂对象的创建过程分离,客户端无需知道复杂对象的内部组成部分与装配方式,只需要知道所需建造者的类型即可.


### 建造者设计模式中的角色
1. *`Builder( 抽象建造者 )` : 它为创建一个产品的各个部件指定抽象接口,在该接口中一般声明两类方法,一类方法是 buildPartX(),其用于创建复杂对象的各个部件. 另一类方法是 getResult(),其用于返回复杂对象. Builder 即可是抽象类,也可以是接口.*
2. *`ConcreteBuilder( 具体建造者 )` : 它实现了 Builder 接口,实现了各个部件的具体构造和装配方法,定义并明确所创建的复杂对象,还可以提供一个用于返回创建好的复杂产品的方法( 该方法可以由抽象构建者实现 ).*
3. *`Product( 产品 )` : 它是被创建的复杂对象,包含多个组成部件,具体建造者创建该产品的内部表示并定义它的装配过程.*
4. *`Director( 指挥者 )` : 指挥者又称为导演类,它负责安排复杂对象的建造次序,指挥者与抽象构建者之间存在关联关系,可以在其 construct() 建造方法中调用建造者对象的部件构造与装配方法,完成复杂对象的建造.*


### 示例程序
*下面是一个使用了建造者设计模式的简单示例程序,其概述为 : 无论是何种游戏角色,都需要逐步创建其组成部分,再将各组成部分配装成一个完整的游戏角色.*

1. *Actor类 : 游戏角色类, 充当复杂产品对象*
```java
package pers.huangyuhui.builder_pattern.builder_pattern_1;

/**
 * @project: design-patterns
 * @description: 游戏角色类, 充当复杂产品对象
 * @author: 黄宇辉
 * @date: 9/19/2019-1:30 PM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public class Actor {
    private String type; //角色类型
    private String sex; //性别
    private String face; //脸型
    private String costume; //服装

    public String getType() {
        return type;
    }

    public void setType(String type) {
        this.type = type;
    }

    public String getSex() {
        return sex;
    }

    public void setSex(String sex) {
        this.sex = sex;
    }

    public String getFace() {
        return face;
    }

    public void setFace(String face) {
        this.face = face;
    }

    public String getCostume() {
        return costume;
    }

    public void setCostume(String costume) {
        this.costume = costume;
    }

    @Override
    public String toString() {
        return "Actor{" +
                "type='" + type + '\'' +
                ", sex='" + sex + '\'' +
                ", face='" + face + '\'' +
                ", costume='" + costume + '\'' +
                '}';
    }
}
```

2. *ActorBuilder抽象类 : 游戏角色建造者, 充当抽象建造者*
```java
package pers.huangyuhui.builder_pattern.builder_pattern_1;

/**
 * @project: design-patterns
 * @description: 游戏角色建造者, 充当抽象建造者
 * @author: 黄宇辉
 * @date: 9/19/2019-1:39 PM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public abstract class ActorBuilder {
    protected Actor actor = new Actor();

    public abstract void buildType();

    public abstract void buildSex();

    public abstract void buildFace();

    public abstract void buildCostume();

    //工厂方法,返回一个完整的游戏角色对象
    public Actor createActor() {
        return actor;
    }
}
```

3. *HeroBuilder类 : 英雄角色建造者, 充当具体建造者*
```java
package pers.huangyuhui.builder_pattern.builder_pattern_1;

/**
 * @project: design-patterns
 * @description: 英雄角色建造者, 充当具体建造者
 * @author: 黄宇辉
 * @date: 9/19/2019-1:42 PM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public class HeroBuilder extends ActorBuilder {
    @Override
    public void buildType() {
        actor.setType("英雄");
    }

    @Override
    public void buildSex() {
        actor.setSex("男");
    }

    @Override
    public void buildFace() {
        actor.setFace("英俊");
    }

    @Override
    public void buildCostume() {
        actor.setCostume("盔甲");
    }
}
```

4. *AngelBuilder类 : 天使角色建造者, 充当具体建造类*
```java
package pers.huangyuhui.builder_pattern.builder_pattern_1;

/**
 * @project: design-patterns
 * @description: 天使角色建造者, 充当具体建造类
 * @author: 黄宇辉
 * @date: 9/19/2019-1:44 PM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public class AngelBuilder extends ActorBuilder {

    @Override
    public void buildType() {
        actor.setType("天使");
    }

    @Override
    public void buildSex() {
        actor.setSex("女");
    }

    @Override
    public void buildFace() {
        actor.setFace("漂亮");
    }

    @Override
    public void buildCostume() {
        actor.setCostume("裙子");
    }
}
```

5. *ActorController类 : 角色控制器, 充当指挥者*
```java
package pers.huangyuhui.builder_pattern.builder_pattern_1;

/**
 * @project: design-patterns
 * @description: 角色控制器, 充当指挥者
 * @author: 黄宇辉
 * @date: 9/19/2019-1:46 PM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public class ActorController {

    //逐步构建复杂产品对象
    public Actor construct(ActorBuilder actorBuilder) {
        Actor actor;
        actorBuilder.buildType();
        actorBuilder.buildSex();
        actorBuilder.buildFace();
        actorBuilder.buildCostume();
        actor = actorBuilder.createActor();
        return actor;
    }
}
```

6. *Client类 : 客户端测试类*
```java
package pers.huangyuhui.builder_pattern.builder_pattern_1;

/**
 * @project: design-patterns
 * @description: 客户端测试类
 * @author: 黄宇辉
 * @date: 9/19/2019-1:49 PM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public class Client {

    public static void main(String[] args) {
        ActorController actorController = new ActorController();

        ActorBuilder zero = new HeroBuilder();
        Actor actor_zero = actorController.construct(zero);
        System.out.println(actor_zero);

        System.out.println("------------------------------");

        ActorBuilder angel = new AngelBuilder();
        Actor actor_angel = actorController.construct(angel);
        System.out.println(actor_angel);
    }
}
```

7. *示例程序的运行结果如下所示 :*
```
Actor{type='英雄', sex='男', face='英俊', costume='盔甲'}
------------------------------
Actor{type='天使', sex='女', face='漂亮', costume='裙子'}
```

#### 示例程序的类图
![](Java设计模式之Builder-Pattern/Builder-Pattern-ClassDiagram1.png)



### 示例程序-简化版
*为了简化系统结构,可以将 Director 和抽象建造者 Builder 进行合并,在 Builder 中提供逐步构建复杂产品对象的 construct() 方法. 由于 Builder 类通常是抽象类,因此可以将 construct() 方法定义为静态方法,以便客户端能够直接调用. 除此之外还有一种更简单的处理方法,既将 construct() 方法中的参数去掉,直接在construct()方法中调用 buildPartX() 方法.( 以下示例程序是基于上述示例程序进行简化的 )*

1. *Actor类 : 游戏角色类, 充当复杂产品对象*
```java
package pers.huangyuhui.builder_pattern.builder_pattern_2;

/**
 * @project: design-patterns
 * @description: 游戏角色类, 充当复杂产品对象
 * @author: 黄宇辉
 * @date: 9/19/2019-1:30 PM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public class Actor {
    private String type; //角色类型
    private String sex; //性别
    private String face; //脸型
    private String costume; //服装

    public String getType() {
        return type;
    }

    public void setType(String type) {
        this.type = type;
    }

    public String getSex() {
        return sex;
    }

    public void setSex(String sex) {
        this.sex = sex;
    }

    public String getFace() {
        return face;
    }

    public void setFace(String face) {
        this.face = face;
    }

    public String getCostume() {
        return costume;
    }

    public void setCostume(String costume) {
        this.costume = costume;
    }

    @Override
    public String toString() {
        return "Actor{" +
                "type='" + type + '\'' +
                ", sex='" + sex + '\'' +
                ", face='" + face + '\'' +
                ", costume='" + costume + '\'' +
                '}';
    }
}
```

2. *ActorBuilder抽象类 : 游戏角色建造者, 充当抽象建造者(省略Director并简化construct方法)*
```java
package pers.huangyuhui.builder_pattern.builder_pattern_2;

/**
 * @project: design-patterns
 * @description: 游戏角色建造者, 充当抽象建造者(省略Director并简化construct方法)
 * @author: 黄宇辉
 * @date: 9/19/2019-1:39 PM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public abstract class ActorBuilder {
    protected Actor actor = new Actor();

    public abstract void buildType();

    public abstract void buildSex();

    public abstract void buildFace();

    public abstract void buildCostume();

    //工厂方法,返回一个完整的游戏角色对象
    //public Actor createActor() {
    //    return actor;
    //}

    //逐步构建复杂产品对象
    public Actor construct() {
        this.buildType();
        this.buildSex();
        this.buildFace();
        this.buildCostume();
        return actor;
    }
}
```

3. *HeroBuilder类 : 英雄角色建造者, 充当具体建造者*
```java
package pers.huangyuhui.builder_pattern.builder_pattern_2;

/**
 * @project: design-patterns
 * @description: 英雄角色建造者, 充当具体建造者
 * @author: 黄宇辉
 * @date: 9/19/2019-1:42 PM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public class HeroBuilder extends ActorBuilder {
    @Override
    public void buildType() {
        actor.setType("英雄");
    }

    @Override
    public void buildSex() {
        actor.setSex("男");
    }

    @Override
    public void buildFace() {
        actor.setFace("英俊");
    }

    @Override
    public void buildCostume() {
        actor.setCostume("盔甲");
    }
}
```

4. *AngelBuilder类 : 天使角色建造者, 充当具体建造类*
```java
package pers.huangyuhui.builder_pattern.builder_pattern_2;

/**
 * @project: design-patterns
 * @description: 天使角色建造者, 充当具体建造类
 * @author: 黄宇辉
 * @date: 9/19/2019-1:44 PM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public class AngelBuilder extends ActorBuilder {

    @Override
    public void buildType() {
        actor.setType("天使");
    }

    @Override
    public void buildSex() {
        actor.setSex("女");
    }

    @Override
    public void buildFace() {
        actor.setFace("漂亮");
    }

    @Override
    public void buildCostume() {
        actor.setCostume("裙子");
    }
}
```

5. *Client类 : 客户端测试类*
```java
package pers.huangyuhui.builder_pattern.builder_pattern_2;

/**
 * @project: design-patterns
 * @description: 客户端测试类
 * @author: 黄宇辉
 * @date: 9/19/2019-1:49 PM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public class Client {

    public static void main(String[] args) {
        Actor zero = new HeroBuilder().construct();
        System.out.println(zero);

        System.out.println("------------------------------");

        Actor angel = new AngelBuilder().construct();
        System.out.println(angel);
    }
}
```

6. *示例程序的运行结果如下所示 :*
```
Actor{type='英雄', sex='男', face='英俊', costume='盔甲'}
------------------------------
Actor{type='天使', sex='女', face='漂亮', costume='裙子'}
```

#### 示例程序的类图
![](Java设计模式之Builder-Pattern/Builder-Pattern-ClassDiagram2.png)
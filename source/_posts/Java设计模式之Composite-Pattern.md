---
title: Java设计模式之Composite Pattern
date: 2019-09-11 12:11:29
tags: [Java,design and pattern]
---

## 学习笔记 : Java设计模式之Composite Pattern
 
### 概述
*`组合设计模式` : 组合多个对象形成树形结构以表示具有`部分-整体`关系的层次结构. 组合模式让客户端可以统一对待单个对象和组合对象. (Composite Pattern : Compose objects into tree structures to pepresent part-whole hierarchies. Composite lets clients treat individual objects and compositions of objects uninformly.)*

* 扩 : 组合模式又称`部分-整体( Part-Whole )`模式,属于对象结构型模式,它将对象组织到树形结构中,可以用来描述整体与部分的关系


### 组合模式中的角色
1. *`Component( 抽象构件 )` : 它可以是接口或抽象类,为叶子构件和容器构件对象声明接口,在该角色中可以包含所有子类共有行为的声明和实现. 在抽象构件中定义了访问及管理它的子构件的方法,如增加,删除,获取子构件等.*
2. *`Leaf( 叶子构件 )` : 它在组合结构中表示叶子结点对象,叶子节点没有子结点,它实现了在抽象构件中定义的行为. 对于那些访问及管理子构件的方法,可以通过抛出异常,提示错误等方式进行处理.*
3. *`Composite( 容器构件 )` : 它在组合结构中表示容器节点对象,`容器结点包含子结点,其子结点可以是叶子结点,也可以是容器结点`,它提供了一个集合用于存储子结点,实现了在抽象构件中定义的行为,包括哪些访问及管理子构件的方法,在其业务方法中可以递归调用其自子结点的业务方法.*

* 扩 : 组合模式的关键在于定义了一个抽象构件类,它既可以代表`叶子`,又可以代表`容器`,客户端针对该抽象构件类进行编程,无须知道它到底表示的叶子还是容器,可以对其进行统一处理. 同时容器对象与抽象构件类之间还建立一个`聚合关联关系`,在容器对象中既可以包含叶子,又可以包含容器,以此实现递归组合,形成一个`树形结构`.


### 适用环境
*Java SE中的 AWT 和 Swing 包的设计就基于组合模式,在这些界面包中为用户提供了大量的容器构件,如 Container( 容器构件 ) 和成员构件,如 Checkbox, Button, TextComponent( 一些叶子构件 )等,其都继承自 Componet 抽象类( 抽象构件 )*


### 示例程序1
*下面是一个使用组合设计模式的简单示例程序,这段示例程序的功能为列出文件夹及其中文件的名称及大小~*

1. *Entry抽象类 : 表示目录条目的抽象类, 充当Component角色*
```java
package pers.huangyuhui.composite_pattern.composite_pattern_1;

/**
 * @project: design-patterns
 * @description: 表示目录条目的抽象类, 充当Component角色
 * @author: 黄宇辉
 * @date: 9/11/2019-12:16 PM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public abstract class Entry {

    public abstract String getName();

    public abstract int getSize();

    public Entry add(Entry entry) throws FileTreatMentException {
        throw new FileTreatMentException();
    }

    public void printList() {
        printList("");
    }

    protected abstract void printList(String prefix);

    public String toString() {
        return getName() + " (" + getSize() + ")";
    }
}
```

2. *File类 : 表示文件的类, 充当Leaf角色*
```java
package pers.huangyuhui.composite_pattern.composite_pattern_1;

/**
 * @project: design-patterns
 * @description: 表示文件的类, 充当Leaf角色
 * @author: 黄宇辉
 * @date: 9/11/2019-12:22 PM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public class File extends Entry {

    private String name;
    private int size;

    public File(String name, int size) {
        this.name = name;
        this.size = size;
    }

    @Override
    public String getName() {
        return name;
    }

    @Override
    public int getSize() {
        return size;
    }

    @Override
    protected void printList(String prefix) {
        System.out.println(prefix + "/" + this.toString());
    }
}
```

3. *Directory类 : 表示文件夹的类, 充当Composite角色*
```java
package pers.huangyuhui.composite_pattern.composite_pattern_1;

import java.util.ArrayList;

/**
 * @project: design-patterns
 * @description: 表示文件夹的类, 充当Composite角色
 * @author: 黄宇辉
 * @date: 9/11/2019-12:25 PM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public class Directory extends Entry {

    private String name;
    private ArrayList<Entry> directory = new ArrayList<>();

    public Directory(String name) {
        this.name = name;
    }

    @Override
    public String getName() {
        return name;
    }

    @Override
    public int getSize() {
        var size = 0;
        for (Entry entry : directory) {
            size += entry.getSize();
        }
        return size;
    }

    @Override
    public Entry add(Entry entry) {
        directory.add(entry);
        return this;
    }

    @Override
    protected void printList(String prefix) {
        System.out.println(prefix + "/" + this.toString());
        for (Entry entry : directory) {
            entry.printList(prefix + "/" + name);
        }
    }
}
```

4. *FileTreatMentException类 : 自定义异常类*
```java
package pers.huangyuhui.composite_pattern.composite_pattern_1;

/**
 * @project: design-patterns
 * @description: 自定义异常类
 * @author: 黄宇辉
 * @date: 9/11/2019-12:19 PM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public class FileTreatMentException extends RuntimeException {

    public FileTreatMentException() {

    }

    public FileTreatMentException(String msg) {
        super(msg);
    }
}
```

5. *Client类 : 客户端测试类*
```java
package pers.huangyuhui.composite_pattern.composite_pattern_1;

/**
 * @project: design-patterns
 * @description: 客户端测试类
 * @author: 黄宇辉
 * @date: 9/11/2019-12:36 PM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public class Client {

    public static void main(String[] args) {
        try {
            System.out.println("------ Making Root Entries ------");
            Directory rootdir = new Directory("root");
            Directory bindir = new Directory("bin");
            Directory temdir = new Directory("tmp");
            Directory usrdir = new Directory("usr");
            rootdir.add(bindir);
            rootdir.add(temdir);
            rootdir.add(usrdir);
            bindir.add(new File("demo1.class", 10000));
            bindir.add(new File("demo2.class", 20000));
            rootdir.printList();

            System.out.println("\n" + "------ Making User Entries ------");
            Directory demoDir1 = new Directory("demoDir1");
            Directory demoDir2 = new Directory("demoDir2");
            Directory demoDir3 = new Directory("demoDir3");
            usrdir.add(demoDir1);
            usrdir.add(demoDir2);
            usrdir.add(demoDir3);
            demoDir1.add(new File("demo.html", 100));
            demoDir1.add(new File("demo.java", 200));
            demoDir2.add(new File("demo.txt", 300));
            demoDir3.add(new File("demo.doc", 400));
            demoDir3.add(new File("demo.mail", 500));
            rootdir.printList();
        } catch (FileTreatMentException e) {
            e.printStackTrace();
        }

    }
}
```

6. *程序运行结果如下所示 :*
```
------ Making Root Entries ------
/root (30000)
/root/bin (30000)
/root/bin/demo1.class (10000)
/root/bin/demo2.class (20000)
/root/tmp (0)
/root/usr (0)

------ Making User Entries ------
/root (31500)
/root/bin (30000)
/root/bin/demo1.class (10000)
/root/bin/demo2.class (20000)
/root/tmp (0)
/root/usr (1500)
/root/usr/demoDir1 (300)
/root/usr/demoDir1/demo.html (100)
/root/usr/demoDir1/demo.java (200)
/root/usr/demoDir2 (300)
/root/usr/demoDir2/demo.txt (300)
/root/usr/demoDir3 (900)
/root/usr/demoDir3/demo.doc (400)
/root/usr/demoDir3/demo.mail (500)
```

#### 示例程序的类图
![](Java设计模式之Composite-Pattern/Composite-Pattern-ClassDiagram1.png)


### 示例程序2
*下面是一个使用组合设计模式的简单示例程序 : 写一个模拟杀毒( Antivirus )程序,该程序既可以对某个文件夹( Folder )杀毒,也可以对某个指定的文件( File )杀毒,而且该杀毒程序还可以根据各类文件的特点为不同类型(文本文件,图像文件,音频文件等)的文件提供不同的杀毒方式.*

1. *AbstractFile抽象类 : 抽象文件类, 充当抽象构件类*
```java
package pers.huangyuhui.composite_pattern.composite_pattern_2;

/**
 * @project: design-patterns
 * @description: 抽象文件类, 充当抽象构件类
 * @author: 黄宇辉
 * @date: 9/11/2019-1:19 PM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public abstract class AbstractFile {

    public abstract String getName();

    public abstract void killVirus();

    public void add(AbstractFile file) {
        throw new FileTreatMentException();
    }

}
```

2. *TextFile类 : 文本文件类, 充当叶子构件类*
```java
package pers.huangyuhui.composite_pattern.composite_pattern_2;

/**
 * @project: design-patterns
 * @description: 文本文件类, 充当叶子构件类
 * @author: 黄宇辉
 * @date: 9/11/2019-1:31 PM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public class TextFile extends AbstractFile {

    private String name;

    public TextFile(String name) {
        this.name = name;
    }

    @Override
    public String getName() {
        return name;
    }

    @Override
    public void killVirus() {
        System.out.println("——————> 对文本文件[ " + name + " ]进行杀毒");
    }
}
```

3. *ImageFile类 : 图像文件类, 充当叶子构件类*
```java
package pers.huangyuhui.composite_pattern.composite_pattern_2;

/**
 * @project: design-patterns
 * @description: 图像文件类, 充当叶子构件类
 * @author: 黄宇辉
 * @date: 9/11/2019-1:27 PM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public class ImageFile extends AbstractFile {

    private String name;

    public ImageFile(String name) {
        this.name = name;
    }

    @Override
    public String getName() {
        return name;
    }

    @Override
    public void killVirus() {
        System.out.println("——————> 对图像文件[ " + name + " ]进行杀毒");
    }
}
```

4. *Folder类 : 文件夹类, 充当容器构件类*
```java
package pers.huangyuhui.composite_pattern.composite_pattern_2;

import java.util.ArrayList;

/**
 * @project: design-patterns
 * @description: 文件夹类, 充当容器构件类
 * @author: 黄宇辉
 * @date: 9/11/2019-1:38 PM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public class Folder extends AbstractFile {

    private String name;
    private ArrayList<AbstractFile> directory = new ArrayList<>();

    public Folder(String name) {
        this.name = name;
    }

    @Override
    public String getName() {
        return name;
    }

    @Override
    public void add(AbstractFile file) {
        directory.add(file);
    }

    @Override
    public void killVirus() {
        System.out.println("======> 对文件夹[ " + name + " ]进行杀毒");
        //递归调用成员构件的killVirus方法
        for (AbstractFile abstractFile : directory) {
            abstractFile.killVirus();
        }
    }
}
```

5. *FileTreatMentException类 : 自定义异常类*
```java
package pers.huangyuhui.composite_pattern.composite_pattern_2;

/**
 * @project: design-patterns
 * @description: 自定义异常类
 * @author: 黄宇辉
 * @date: 9/11/2019-12:19 PM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public class FileTreatMentException extends RuntimeException {

    public FileTreatMentException() {

    }

    public FileTreatMentException(String msg) {
        super(msg);
    }
}
```

6. *Client类 : 客户端测试类*
```java
package pers.huangyuhui.composite_pattern.composite_pattern_2;

/**
 * @project: design-patterns
 * @description: 客户端测试类
 * @author: 黄宇辉
 * @date: 9/11/2019-1:44 PM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public class Client {

    public static void main(String[] args) {
        AbstractFile folder = new Folder("my-folder");
        AbstractFile textFolder = new Folder("text-folder");
        AbstractFile imageFolder = new Folder("image-folder");
        AbstractFile file = new TextFile("demo.text");
        AbstractFile image = new ImageFile("demo.jpg");
        textFolder.add(file);
        imageFolder.add(image);
        folder.add(textFolder);
        folder.add(imageFolder);
        folder.killVirus();
    }

}
```

7. *示例程序的运行结果如下所示 :*
```
======> 对文件夹[ my-folder ]进行杀毒
======> 对文件夹[ text-folder ]进行杀毒
——————> 对文本文件[ demo.text ]进行杀毒
======> 对文件夹[ image-folder ]进行杀毒
——————> 对图像文件[ demo.jpg ]进行杀毒
```

#### 示例程序的类图
![](Java设计模式之Composite-Pattern/Composite-Pattern-ClassDiagram2.png)

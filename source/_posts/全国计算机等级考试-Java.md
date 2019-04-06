---
title: 全国计算机等级考试-Java
date: 2019-03-15 11:10:42
tags: [NCRE,Java]
---

### NCRE(National Computer Rank Examination) —— 二级`Java`篇

- ![ ](全国计算机等级考试-Java/excitement.jpg)

**`敲黑板：二级考试的试题都是在往年的试卷中随机抽取的 ! 勿慌 ✧⁺⸜(●˙▾˙●)⸝⁺✧`**

- ### 选择题总结(`重点+易错点`)
1. *try与catch的匹配是: catch到的异常是try中可能抛出的异常或其子类的实例.*

2. *按照标准的I/O模型,Java提供了三种标准流,分别为：`System.in` , `System.out` , `System.err`.*

3. *属于过滤字节输出流的类是：`BufferedOutputSteam`*.

4. *当窗口被激活时，调用的窗口事件的方法是：`public void windowActivated(WindowEvent)`*

5. *对类进行并发控制，需要私有属性进行定义变量(private),用锁将方法锁起来`synchronized`,这样就不会出现并发出现异常情况.*

6. *`Swing`组件的外观与具体平台无关.*

7. *在Java中的开发包1.7之前，switch不支持`String`类型数据,case值和key值匹配时,程序会报错 !*

8. *在一个类中可以定义为多个名称相同,但参数不同的方法,这种机制叫做：重载.*

9. *匿名内部类也就是没有名字的内部类,正因为没有名字,所以匿名内部类只能使用一次!它通常用来简化代码编写,但使用匿名内部类还有一个前提条            件：`必须继承一个父类或实现一个接口 !`*

10. *类变量必须带有的修饰符为：static*

11. *在Java中二维数组定义的语法为：数据类型[][]数组名=new 数据类型[行数][列数];*

12. *在Java.io包的字节输入流中,过滤器输入流的基类是: `FilterInputStream`*

13. *所有字符输入流都从`Reader`类继承,所有字符输出流都从`Writer`类继承.*

14. *Swing除继续沿用了AWT中的`FlowLayout,BorderLayout,CardLayout,GridLayout,GridBagLayout`布局管理器外,新增了一个`BoxLayout`布局管理      器.`BoxLayout`布局管理器按照自上而下(y轴)或者从左到右(x轴)的顺序布局依次加入构件.*

15. *java.awt.event包中定义了适配器类；该包中定义的适配器类包括以下几个:* 
    - `ComponentAdapter 构件适配器`
    - `ContainerAdapter 容器适配器`
    - `FocusAdapter 焦点适配器`
    - `MouseAdapter 鼠标适配器`
    - `KeyAdapter 键盘适配器`
    - `MouseMotionAdapter 鼠标运动适配器` 
    - `WindowAdapters 窗口适配器`
    - `而FrameAdapter 不属于事件适配器类 !`*

16. *如果发生下面几种情况时,处于运行状态的线程就进入阻塞状态 :*
    - 线程调用`sleep();` / `join();` / `wait();`方法.
    - 如果线程中使用`sychronized`来请求对象的所未获得时.
    - 如果线程中有输入/输出操作,则进入阻塞状态;待输入/输出操作结束后,线程进入可运行状态.
  
17. *在Java中,使用`synchronized`关键字标识临界区.Java平台将每个由`synchronized`语句设置的对象设置一个锁,称为`对象锁`,它是一种独占的排        他锁,既同一时刻最多只能有一个线程获取该锁,为了能够正常地使用对象锁，对共享数据的所有访问都必须在临界区内.同时临界区的共享数据必须是私       有的,确保只能通过对象的方法才能访问到.* 

18. *算法的空间复杂度是指算法在执行过程中所需的内存空间.算法执行期间所需的存储空间包括3个部分:*
    - 输入数据的存储空间
    - 程序本身所占的存储空间
    - 算法执行过程中所需要的额外空间
  
19. *`需求分析`是对待开发软件提出的需求进行分析并给出详细设计,是解决软件做什么的阶段.*

20. *软件设计可以使用的工具有`总体结构图`,`程序流程图`,`N-S图`,`PAD图`..注意：`数据流程图(DFD)`是需求分析使用的工具 !*

21. *逻辑设计阶段形成逻辑数据模型(从`E-R图`向`关系模式`转换).注意点：关系模式设计属于：`逻辑设计`*

22. *能够运行Java字节码的命令为: `javac`*

23. *`break`在java起到跳出循环的作用.`continue`是跳过当次循环继续执行下一次循环.*

24. *`XML`只是一个配置文件通过流将XML件读取出来，并不属于字节流 !*

25. *`yield()`该方法与`sleep()`类似,只是不能由用户指定暂停多长时间,并且`yield()`方法只能让同优级的线程有执行的机会 !*

26. *javap的作用: `Javap`是 Java class文件分解器,可以反编译,也可以查看java编译器生成的字节码.*

27. *在数据库设计中,在需求分析阶段建立数据字典.*

28. *注意: 在 ` / ` 运算中并不存四舍五入的操作!只取整数部分!*

29. *正则表达式是一种描述某种匹配检索规则的字符串.*
    - java.util.regex 包中有一个`Pattern`类,用于进行快速模拟匹配.
    - java.util.regex 包还具有一个`Matcher`匹配器类. 
  
30. *`javax.swing.text`是文本类包,`javax.swing.table`是表格类包,`javax.swing.plaf`包括一些对外观样式操作的类和接口.*

31. *`JFrame`是页面容器,`JToolBar`为工具条(属于容器的一种).*

32. *Java中的线程包含`CPU`,`代码`,`数据`三个部分*

33. *`interrupt();`中断线程:中断状态被清除. `start();`用于开启一个线程. `notify();`用于唤醒一个处于等待状态的线程.*

34. *当一个Applet所在的浏览器被最小化以后,不能被调用的Applet方法是`init()`*
    - `init();` 当浏览器加载applet,进行初始化的时候调用该方法.
    - `start();` 该方法在`init()`方法之后调用,当用户从其它页面转到包含applet的页面时该方法也被调用.
    - `stop();` 在用户离开包含applet的页面时被调用.
    - `destroy();` 当applet不再被使用,或浏览器退出的时候,该方法被调用.
  
--- 

  - ### 操作题总结(`重点+API介绍`)
  1. *数组的算法排序继承于 `Comparable`*
  
  2. *JApplet(`init()`方法中接收来自html页面上的参数).*
  
  3. *通过`setName(String str);`为继承Thread的子类的线程初始化名字.*
  
  5. *`equals();`方法比较的是两个字符串的内容是否相等.而 `==` 符号比较的是: 两个字符串的地址是否相同.*
  
  6. *当程序中涉及鼠标点击事件需要继承`MouseAdapter`类来对鼠标做出的动作进行相应的回应 !*
  
  7. *`paint(Graphics g)`,调用 `g.drawOval();`可以画同心圆.*
  
  8. *Applet中绘制指定文本字符串使用：`drawString();`*
  
  9. *Java中String类提供了`charAt();`方法遍历每个字符.* 
  
  10. *常用布局管理器*
      - `FlowLayout`,`BorderLayout`,`GridLayout`,`CardLayout`,`GridBagLayout`.
      - 注意: `JLabel`对象可以显示文本,图像或同时显示两者,创建对象时,在构造函数中输入要显示的对象即可.
  
  11. *`getContentPane();`方法获得JFrame的内容面板. `getSource();` 找到给定名称的资源*.
    
  12.  *`frame.pack();-Method in class java.awt. Window.` 使此窗口的大小适合其子组件的首选大小和布局.*
  
  13.  *`JPanel`是Java图形用户界面(GUI)工具包swing中的面板容器类,包含于javax.swing包,是一种轻量级容器,可以加入到`JFrame`窗体中,所以我们       需要继承此类作为画板供JFrame类来操作*
  
  14.  *通过鼠标点击获取相应的请求命令方法为: `e.getActionCommand();`*

  15. *`beep() - Method in class java.awt. Toolkit`. 根据本地系统设置和硬件功能发出音频蜂鸣声.*
      - for example: Toolkit.getDefaultToolkit().beep(); 
  
  16. *`drawRect(int, int, int, int) - Method in class java.awt.Graphics` 绘制指定矩形的轮廓.*
  
  17. *`DataOutputStream` 数据输出流允许应用程序以机器无关方式将Java基本数据类型写到底层输出流,
       `DataInputStream` 类提供`readChar();`方法来读取文件中的字符.*

  18. *`Java IO`*
```java
    System.getProperty("user.dir");     获取当前的工作路径.
	getName();                          方法获取文件名，getPath();获取文件路径.
	isDirectory();                      判断是否是文件.
```
 

  19. *`extends Thread` 与 `implements Runnable` 使用*
```java
    当通过`implements Runnable`创建线程:将实现了Runnable接口的对象作为`target`参数传递给Thread类即可,格式如下:
	new Thread(`实现了Runnable接口的对象名`);
	举例:	
		public class Java_2 implements Runnable { 
  		    public static void main(String[]args) {
      			Java_2 r = new Java_2();
      		        Thread t = new Thread( r );
     			       t.start(); 
  			 }···

    通过`extends Thread`创建线程:
	Thread本身实现了`Runnable`接口,重写其`run()`方法即可;线程创建后,需要调用`start();`方法运行.
```


  20. 
```java
    setPreferredSize(new Dimension(280, 60));
    public void setPreferredSize(Dimension preferredSize){...}

    将此组件的首选大小设置为常量值. 随后`getPreferredSize()`总是返回此值.将首选大小设置为`null`可恢复默认行为. 
    `Dimension` : 类封装单个对象中组件的宽度和高度(以整数精度).
```


  21.  *`DecimalFormat`类提供的`format();`方法可以将数据格式化.*
```java
    public class DecimalFormat extends NumberFormatDecimalFormat(){...}

    `DecimalFormat`是格式为十进制数的NumberFormat的具体子类. 它具有各种功能,旨在使任何地区的数字解析和格式化,包括支持西方,阿拉伯语和印度数字。它还支持不同类型的数字,包括整数(123..),定点数(123.4),科学记数法(1.23E4),百分比(12％),和货币金额(123美元),所有这些都可以进行本地化.
```


  22. *窗体事件*
```java
    setDefaultCloseOperation();     设置用户在此窗口上发起"close"时默认执行操作.
    JFrame.EXIT_ON_CLOSE;           `close`式关闭程序退出窗口.

    for example: frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);   
```

  23. *常用事件类*
```java
    public void addActionListener(ActionListener e){..}
    添加指定的动作侦听器以从此按钮接收动作事件.当用户按下或释放鼠标在此按钮上时,会发生动作事件.如果`e`为空,则不会抛出任何异常.
    也不会执行任何操作.

    for example: saveButton.addActionListener(this); 
```


--- 


- **哎哟终于写好了 ! 伸个懒腰 ٩(๑❛ᴗ❛๑)۶ ~ 最近没零花钱买`麦斯威尔`了,然后每天的状态就像下面的俺的小黄人一样  ╰( ´・ω・)つ──☆✿✿✿**

- ![ ](全国计算机等级考试-Java/awkwardness.jpg)

- ##### 后期有时间还写推出`NCRE-MySQL`,`NCRE-C`,`NCRE-三级数据库`等全国计算机等考题目解析 ! 最近忙于备考,所以先写作先放一放 ~
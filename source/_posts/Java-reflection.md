---
title: Java reflection
date: 2019-04-15 08:39:07
tags: [Java]
---

## Java Reflection

### 概念
*`Reflection(反射)`是被认为`动态语言`的关键,反射机制允许程序在执行期间借助于`Reflection API`取得任何类的内部信息,并能直接操作任意对象的内部属性及方法.*  

### 应用
*动态代理*


### 反射机制提供的功能
1. 在运行时判断任意一个对象所属的类.
2. 在运行时构造人任意一个类的对象.
3. 在运行时判断任意一个类所具有的成员变量和方法.
4. 在运行时调用任意一个对象的成员变量和方法.
5. 生成`动态代理`(体现`动态语言`的特性).


#### 反射相关的主要`API`
- *java.lang.Class : 代表一个类.*
- *java.lang.reflect.Method : 代表类的方法.*
- *java.lang.reflect.Field : 代表类的成员变量.*
- *java.lang.reflect.Constructor : 代表类的构造方法.*
- *...*


#### 反射的简单应用示例
1. *首先创建一个存储用户信息的类*
```java
package pers.huangyuhui.reflection.bean;

/**
 * @ClassName: UserInfo
 * @Description: 用户信息
 * @author: HuangYuhui
 * @date: Apr 15, 2019 9:03:40 AM
 * 
 */
public class UserInfo {

	public String name;
	private String sex;
	private int age;

	public UserInfo() {
		super();
	}

	public UserInfo(String name, String sex, int age) {
		super();
		this.name = name;
		this.sex = sex;
		this.age = age;
	}

	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}

	public String getSex() {
		return sex;
	}

	public void setSex(String sex) {
		this.sex = sex;
	}

	public int getAge() {
		return age;
	}

	public void setAge(int age) {
		this.age = age;
	}

	@Override
	public String toString() {
		return "UserInfo [name=" + name + ", sex=" + sex + ", age=" + age + "]";
	}

	public void showClassInfo() {
		System.out.println("ClassInfo: Store the user information .");
	}

	public void introduce(String s) {
		System.out.println(s + "  my name is: " + name + "  age: " + age + "  sex: " + sex);
	}

}
```
2. *其次利用`反射`获取该类中的信息*
```java
package pers.huangyuhui.reflection.bean.test;

import java.lang.reflect.Field;
import java.lang.reflect.InvocationTargetException;
import java.lang.reflect.Method;

import org.junit.Ignore;
import org.junit.jupiter.api.Test;

import pers.huangyuhui.reflection.bean.UserInfo;

/**
 * @ClassName: UserInfoTest
 * @Description: 用`反射`获取`UserInfo`类中信息
 * @author: HuangYuhui
 * @date: Apr 15, 2019 9:10:08 AM
 * 
 */
public class TestUseInfoByReflection {

	// 不使用反射 : 创建`UserInfo`类对象,并调用其中的属性及方法
	// @Test
	@Ignore
	public void commonTest() {
		UserInfo userInfo = new UserInfo();
		userInfo.setName("Java reflection");
		userInfo.setAge(1);
		userInfo.setSex("Male");
		userInfo.showClassInfo();
		userInfo.introduce("Hello !");
	}

	// 使用反射: 创建`UserInfo`类对象,并调用其中的属性及方法
	@Test
	public void reflectionTest() throws InstantiationException, IllegalAccessException, NoSuchFieldException,
			SecurityException, NoSuchMethodException, IllegalArgumentException, InvocationTargetException {

		// 下述两行代码体现了: `UserInfo userInfo = new UserInfo();`的工作
		Class<UserInfo> class1 = UserInfo.class;
		// 创建运行时类`UserInfo`类的对象
		UserInfo userInfo = class1.newInstance();

		System.out.println(userInfo);

		/*
		 * 通过`getField`调用别`public`修饰的`name`属性并为其赋值
		 */
		java.lang.reflect.Field fieldName = class1.getField("name");// `name`被`public`修饰,否则抛出: NoSuchFieldException
		fieldName.set(userInfo, "Java reflection");

		System.out.println(userInfo);

		/*
		 * 通过`getField`调用被`private`修饰的`age,sex`属性并为其赋值
		 */
		Field fieldAge = class1.getDeclaredField("age");
		Field fieldSex = class1.getDeclaredField("sex");
		// 将此对象的 accessible 标志设置为指示的布尔值.值为 true 则指示反射的对象在使用时应该取消 Java 语言访问检查.
		// 值为 false 则指示反射的对象应该实施 Java 语言访问检查.实际上setAccessible是启用和禁用访问安全检查的开关,
		// 并不是为true就能访问为false就不能访问 ! false时使用对象会抛出 : IllegalAccessException .
		// 由于JDK的安全检查耗时较多,所以通过`setAccessible(true)`的方式关闭安全检查就可以达到提升反射速度的目的 .
		fieldAge.setAccessible(true);
		fieldSex.setAccessible(true);
		fieldAge.set(userInfo, 2);
		fieldSex.set(userInfo, "male");

		System.out.println(userInfo);

		/*
		 * 通过``调用对象指定的方法
		 */
		java.lang.reflect.Method method = class1.getMethod("showClassInfo");
		// return: the result of dispatching the method represented bythis object on obj
		// with parameters args.
		method.invoke(userInfo);
		Method method2 = class1.getMethod("introduce", String.class);
		method2.invoke(userInfo, "Hello !");

	}
}
```
3. *程序运行结果如下*
```java
UserInfo [name=null, sex=null, age=0]
UserInfo [name=Java reflection, sex=null, age=0]
UserInfo [name=Java reflection, sex=male, age=2]
ClassInfo: Store the user information .
Hello !  my name is: Java reflection  age: 2  sex: male
```

### 详解反射的源头 : `Class`

*在`Object`类中定义了以下的方法,此方法被所有子类继承 :*
```java
@HotSpotIntrinsicCandidate
    public final native Class<?> getClass();
```
*该返回值的类型是一个`Class`类,此类是`Java反射`的源头,实际上所谓的反射从程序的运行结果来看也也很理解,既: `可以通过对象反射求出类的名称.`*
- *正常方式 : 引入需要的`包类`名称 ——> 通过`new`实例化 ——> 取得实例化对象*
- *反射方式 : 实例化对象 ——> `getClass()` ——> 得到完整的`包类`名称*


#### `getClass()`的使用
1. *示例程序如下*
```java
package pers.huangyuhui.reflection.bean.test;

import org.junit.jupiter.api.Test;
import pers.huangyuhui.reflection.bean.UserInfo;

/**
 * @ClassName: TestUserInfoByReflection2
 * @Description: 学习反射源头: `Class`类
 * @author: HuangYuhui
 * @date: Apr 15, 2019 11:58:34 AM
 * 
 */
public class TestUserInfoByReflection2 {

	@Test
	public void getClassTest() {

		UserInfo userInfo = new UserInfo();
		// getClass() return: The Class object that represents the runtimeclass of this object.
		// 通过`运行时类`的对象,调用其`getClass()`返回其运行时类
		Class<?> class1 = userInfo.getClass();
		System.out.println(class1);
	}
}
```
2. *程序运行结果如下*
```java
class pers.huangyuhui.reflection.bean.UserInfo //`运行时`类
```
- *`运行时类` : example.java ——`编译器(javac.exe)`——> example.class ——`JVM类加载器`——> example.class被加载到内存(此时`example.class`就是一个`运行时类`,存放在`缓存区`,且运行时类本身就是一个`Class`实例并只加载一次 !)*


#### 获取`Class`类的实例的四种方式
*获取`Class`类的实例后我们可以进行如下的操作:*
1. 创建对应的运行时类的对象.
2. 调用对应的运行时类的完整结构( 属性,方法,构造器 ).
3. 获取对应的运行时类的完整结构( 属性,方法,构造器,父类,所在的包,异常,注解... ).

*获取`Class`类实例的四种方式的示例程序如下*
```java
package pers.huangyuhui.reflection.bean.test;

import org.junit.Test;
import pers.huangyuhui.reflection.bean.UserInfo;

/**
 * @ClassName: GetClassObj
 * @Description: 获取`Class`类实例的四种方式
 * @author: HuangYuhui
 * @date: Apr 15, 2019 12:34:21 PM
 * 
 */
public class GetClassObj {

	// @Test
	// 第一种方式: 通过调用运行时类本身的`.class`属性
	public void firstWayTest() {

		Class<UserInfo> class1 = UserInfo.class;
		Class<String> class2 = String.class;

		// the result: class pers.huangyuhui.reflection.bean.UserInfo
		System.out.println(class1);
		// the result: class java.lang.String
		System.out.println(class2);

	}

	// @Test
	// 第二种方式: 通过`运行时类`的对象获取
	public void secondWayTest() {

		UserInfo userInfo = new UserInfo();
		Class<? extends UserInfo> class1 = userInfo.getClass();

		// getName() return: the name of the class or interfacerepresented by this object.
		// the result: pers.huangyuhui.reflection.bean.UserInfo
		System.out.println(class1.getName());

	}

	// @Test
	// 第三种方式: 通过`Class`的静态方法获取
	public void thirdWayTest()
			throws ClassNotFoundException, NoSuchMethodException, SecurityException, NoSuchFieldException {

		String className = "pers.huangyuhui.reflection.bean.UserInfo";
		Class<?> class1 = Class.forName(className);

		// the result: public java.lang.String
		// pers.huangyuhui.reflection.bean.UserInfo.name
		System.out.println(class1.getField("name"));
	}

	@Test
	// 第四种方式: 通过类加载器获取
	public void fourthWayTest() throws ClassNotFoundException, NoSuchMethodException, SecurityException {

		String className = "pers.huangyuhui.reflection.bean.UserInfo";

		ClassLoader classLoader = this.getClass().getClassLoader();
		// Invoking this method is equivalentto invoking loadClass(name,false).
		Class<?> class1 = classLoader.loadClass(className);

		// the result: public void pers.huangyuhui.reflection.bean.UserInfo.introduce(java.lang.String)
		System.out.println(class1.getMethod("introduce", String.class));
	}

}
```

### 类加载器`ClassLoader`

#### 概念
*类加载器的作用:把`类(class)`装载进内存.如下示例*
```java
Java源文件(*.java) ——Java编译器——> 字节码文件(*.class) ——类加载器+字节码效验器+解释器——> 操作系统平台
```
*`JVM`规范定义了两种类型的类加载器:`启动类加载器(bootstrap)`和`用户自定义加载器(user-defined class loader)`.JVM在运行时会产生`三个类加载器`*
1. 引导类加载器.
- *`JVM`自带的类加载器,负责Java平台核心库,用来加载核心类库,该加载器无法直接获取.*
2. 扩展类加载器.
- *负责`jar包`或将指定目录下的`jar包`装入工作库.*
3. 系统类加载器.
- *负责`java-classpath`或`java.class.path`所指的目录下的类与`jar`包装入工作.(最常用)*

*`三个类加载器`的示例程序如下*
```java
package pers.huangyuhui.reflection.classloader;

import org.junit.Test;
import pers.huangyuhui.reflection.bean.UserInfo;

/**
 * @ClassName: ClassLoader
 * @Description: 类加载器`Classloader`
 * @author: HuangYuhui
 * @date: Apr 15, 2019 5:37:30 PM
 * 
 */
public class TestClassLoader {

	@Test
	public void systemClassLoaderTest() throws ClassNotFoundException {

		/*
		 * 获取`系统类加载器`
		 */
		ClassLoader classLoader = ClassLoader.getSystemClassLoader();
		// getName() Returns: name of this class loader,or null if this class loader is
		// not named.
		// the result: jdk.internal.loader.ClassLoaders$AppClassLoader@28c97a5
		System.out.println(classLoader);

		/*
		 * 获取`系统类加载器`的父类加载器(`平台类加载器: getPlatformClassLoader`)
		 */
		ClassLoader classLoader2 = classLoader.getParent();
		// the result: jdk.internal.loader.ClassLoaders$PlatformClassLoader@7bedc48a
		System.out.println(classLoader2);

		/*
		 * 查看自定义类`UserInfo`由哪个加载器加载
		 */
		Class<UserInfo> class1 = UserInfo.class;
		ClassLoader classLoader3 = class1.getClassLoader();
		// the result: jdk.internal.loader.ClassLoaders$AppClassLoader@28c97a5
		// 由运行结果可知,该自定义类被`系统加载器`加载
		System.out.println(classLoader3);

		/*
		 * 查看`java.lang.Object`(核心类库)由哪一个加载器加载
		 */
		Class<?> class2 = Class.forName("java.lang.Object");
		ClassLoader classLoader4 = class2.getClassLoader();
		// the result: null
		// 运行结果为`null`,说明该类被`引导类加载器`加载 !
		// `引导类加载器`: `JVM`自带的类加载器,负责Java平台核心库,用来加载核心类库,该加载器无法直接获取
		System.out.println(classLoader4);

	}
}
```

#### 类的加载过程
*当程序主动使用某个类时,如果该类还未被`加载`到内存中,则系统会通知如下三个步骤来对该类进行初始化.*
1. 类的加载 : 将类的`Class`文件读入到内存,并为之创建一个`java.lang.Class`对象,此过程由类加载器完成.
2. 类的链接 : 将类的`二进制`数据合并到`JRE`中.
3. 类的初始化 : `JVM`负责对类进行初始化.


#### 类加载器的应用
1. *利用`ClassLoader`读取指定`包`下的配置文件(如果不使用类加载器配置文件只能放在`项目`目录下..).程序示例如下 :*
```java
package pers.huangyuhui.reflection.classloader;

import java.io.IOException;
import java.io.InputStream;
import java.util.Properties;

import org.junit.Test;

/**
 * @ClassName: ReadConfigFileInfo
 * @Description: 利用`ClassLoad`
 * @author: HuangYuhui
 * @date: Apr 15, 2019 6:29:39 PM
 * 
 */
public class ReadConfigFileInfo {

	@Test
	public void classLoaderTest() throws IOException {

		Properties properties = new Properties();

		// 如果不使用`ClassLoader`,读取当前工程下的配置文件的方式如下:
		// FileInputStream fileInputStream = new FileInputStream(new File("user.properties"));

		ClassLoader classLoader = this.getClass().getClassLoader();
		// return: An input stream for reading the resource.
		InputStream inputStream = classLoader
				.getResourceAsStream("pers\\huangyuhui\\reflection\\classloader\\user.properties");

		// Reads a property list (key and element pairs) from the inputbyte stream.
		properties.load(inputStream);

		System.out.println("singer: " + properties.getProperty("singer") + "\nsong: " + properties.getProperty("song"));

	}

}
```
2. *在`pers.huangyuhui.reflection.classloader`包下创建配置文件`user.properties`*
```java
singer=Justin Bieber
song=Love yourself
```

3. *程序运行结果如下*
```java
singer: Justin Bieber
song: Love yourself
```

### `newInstance()`创建运行时类的对象

#### 条件
*创建类的对象: 调用`Class`对象的`newInstance()`方法,要求如下：*
1. 类必须有一个无参数的构造器.
2. 类的构造器的访问权限需要足够.

#### 示例程序
```java
package pers.huangyuhui.reflection.newinstance;

import org.junit.Test;

import pers.huangyuhui.reflection.bean.UserInfo;

/**
 * @ClassName: NewInstanceTest
 * @Description: 使用`newInstance()`创建运行时类的对象
 * @author: HuangYuhui
 * @date: Apr 16, 2019 10:08:37 PM
 * 
 */
public class NewInstanceTest {

	@Test
	public void test() throws InstantiationException, IllegalAccessException, ClassNotFoundException {

		String className = "pers.huangyuhui.reflection.bean.UserInfo";
		Class<?> class1 = Class.forName(className);

		@SuppressWarnings("deprecation")
		// 使用`newInstance()`创建对应的运行时类的对象时实际上是调用了运行时类的空参构造器.
		UserInfo userInfo = (UserInfo) class1.newInstance();

		// the result: UserInfo [name=null, sex=null, age=0]
		System.out.println(userInfo);
	}

}
```

### 通过反射调用类的`完整结构`

1. 获取属性
- *略*
2. 获取方法
- *略*
3. 获取构造器
- *略*

### 反射的应用之`动态代理`

#### `动态代理`介绍
1. 概念
*动态代理是指客户通过代理类来调用其它对象的方法,并且是在程序运行时根据需要动态创建目标类的代理对象.*
2. 使用场合
*调试及远程方法调用等..*
3. 设计模式的原理
*使用一个代理将对象包装起来,然后使用该代理对象取代原始对象.任何对原始对象的调用都要通过代理.代理对象决定是否以及何时将方法调用转到原始对象上.*

#### `静态代理`程序示例
*为了与动态代理相比较,静态代理程序示例如下.不难发现: 如果添加不同接口则需要不同的代理类来完成其代理!很繁琐!!*
```java
package pers.huangyuhui.reflection.proxy;

interface ClothFactory {
	void createCloth();
}

//被代理类
class NikeClothFactory implements ClothFactory {

	@Override
	public void createCloth() {
		System.out.println("Create the cloth ..");
	}
}

//代理类
class ProxyFactory implements ClothFactory {

	NikeClothFactory nikeClothFactory;

	public ProxyFactory(NikeClothFactory nike) {
		this.nikeClothFactory = nike;
	}

	@Override
	public void createCloth() {
		System.out.println("This is static proxy ..");
		nikeClothFactory.createCloth();
	}

}

/**
 * @ClassName: StaticProxy
 * @Description: 静态代理
 * @author: HuangYuhui
 * @date: Apr 18, 2019 10:31:27 AM
 * 
 */
public class StaticProxy {

	public static void main(String[] args) {

		NikeClothFactory nikeClothFactory = new NikeClothFactory();
		ProxyFactory proxyFactory = new ProxyFactory(nikeClothFactory);
		proxyFactory.createCloth();

		//程序运行结果如下所示 :
		// This is static proxy ..
		// Create the cloth ..
	}
}
```

#### `动态代理`程序示例

- *`反射`是动态语言的关键,程序示例如下 :*
```java
package pers.huangyuhui.reflection.proxy;

import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;

interface Subjet {
	void action();
}

//被代理类
class RealSubject implements Subjet {

	@Override
	public void action() {
		System.out.println("被代理类执行的动作 ..");
	}
}

//实现动态代理的核心类
class MyInvocationHandler implements InvocationHandler {

	Object obj;// 实现了接口的被代理类的对象的声明

	// 给被代理的对象实例化
	public Object dynamicInit(Object object) {
		this.obj = object;
		// `动态`地创建指定代理类的对象并返回
		return Proxy.newProxyInstance(obj.getClass().getClassLoader(), obj.getClass().getInterfaces(), this);
	}

	@Override
	// 当通过代理类的对象发起对被重写的方法调用时,都会转换为对如下`invoke`方法的调用
	public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {

		Object object = method.invoke(obj, args);
		return object;
	}

}

/**
 * @ClassName: DynamicProxy
 * @Description: 动态代理
 * @author: HuangYuhui
 * @date: Apr 18, 2019 11:03:54 AM
 * 
 */
public class DynamicProxy {

	public static void main(String[] args) {

		MyInvocationHandler myInvocationHandler = new MyInvocationHandler();
		RealSubject realSubject = new RealSubject();

		// `dynamicInit`方法动态的返回一个同样实现了 `Real ...`所在类实现的接口`Subject`的代理类的对象
		Object object = myInvocationHandler.dynamicInit(realSubject);
		// 此时`subject`为代理类的对象
		Subjet subjet = (Subjet) object;
		// 调用`action`时会转到对`InvocationHandler`接口的实现类的`invoke`方法的调用
		subjet.action();

		// 为证明动态代理的特性,再实现一个接口的代理类的对象
		NikeClothFactory nikeClothFactory = new NikeClothFactory();
		ClothFactory clothFactory = (ClothFactory) myInvocationHandler.dynamicInit(nikeClothFactory);
		clothFactory.createCloth();

		//程序运行结果如下所示 :
		//被代理类执行的动作 ..
		//Create the cloth ..
	}

}
```
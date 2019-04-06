---
title: static keyword
date: 2019-03-16 22:03:17
tags: [Java,JUnit]
---

### 今天刷C语言程序题时遇到很多关于`static`关键字的题目,为区分Java中的`static`继而回到寝室写一篇关于Java中`static`关键字的使用详解.

### 先来谈谈Java中的`static`
```java
"static方法就是没有this的方法.在static方法内部不能调用非静态方法,反过来是可以的.而且可以在没有创建任何对象的前提下,仅仅通过类本身来调用static方法.这实际上正是static方法的主要用途.   ———— 《 Java编程思想 》"

总结为一句话：方便在没有创建对象的情况下来进行调用(方法/变量).
```

- #### `static` 变量
```java
static 变量也称作静态变量.
静态变量:   静态变量被所有的对象所共享,在内存中只有一个副本.它当且仅当在类初次加载时会被初始化.
非静态变量: 是对象所拥有的,在创建对象的时候被初始化.存在多个副本,各个对象拥有的副本互不影响.
```

- #### `static` 方法
```java
使用 static 修饰的方法一般称作静态方法.由于静态方法不依赖于任何对象就可以进行访问,因此对于静态方法来内说是无法使用 this 关键字的,
因为它不依附于任何对象,既然都没有对象,就谈不上 this 了 ! 继而由于这个特性,在静态方法中不能访问类的非静态成员变量和非静态成员方法,
因为非静态成员方法/变量都是必须依赖具体的`对象`才能够被调用.
```

- #### `static` 代码块
```java
静态代码块以优化程序性能.static 块可以置于类中的任何地方,类中可以有多个static块.
在类初次被加载的时候,会按照 static 代码块的顺序逐个执行,并且只会执行一次.
```
#### 使用JDBC中简单的`Data access obejct`实例来讲解如何利用`static`关键字优化程序性能 ! (っ•̀ω•́)っ✎⁾⁾ 

1. 首先创建一个连接数据库的工具类,使用`static 代码块`解决重复加载驱动问题,提高性能.
```java
/**
 * @ClassName: JDBCUtil
 * @Description: Connect to the database.
 * @author: Huang Yuhui
 * @date: Mar 13, 2019 3:19:41 PM
 */
public class JDBCUtil {
	
	private JDBCUtil() {
		// TODO Auto-generated constructor stub
	}

	// initialize the driver of database.
	static {
		try {
			Class.forName("com.mysql.cj.jdbc.Driver");
		} catch (ClassNotFoundException e) {

			e.printStackTrace();
		}
    }

	// Return a database connection object.
	public static Connection getConnection() {
		Connection connection = null;
		try {
			connection = DriverManager.getConnection("jdbc:mysql://localhost/databaseName?"
					+ "useSSL=false&serverTimezone=UTC&allowPublicKeyRetrieval=true", "username", "password");
		} catch (SQLException e) {
			e.printStackTrace();
		}
		return connection;
	}
	
	// release the resource of connecting.
	public static void freeResource(Connection connection,PreparedStatement preparedStatement,ResultSet resultSet) {
		if(connection!=null) {
			try {
				connection.close();
			} catch (SQLException e) {
				
				e.printStackTrace();
			}
		}
		if(preparedStatement!=null) {
			try {
				preparedStatement.close();
			} catch (SQLException e) {
	
				e.printStackTrace();
			}
		}
		if(resultSet!=null) {
			try {
				resultSet.close();
			} catch (SQLException e) {
				
				e.printStackTrace();
			}
		}
	}
}
```

2. 利用`static`修饰单独抽取出来的`Connection`对象使其成为静态变量.继而使得整个`DAO`可以共享此对象.
```java
/**
 * @ClassName: basicConnection
 * @Description: Share this object of Connection.
 * @author: Huang Yuhui
 * @date: Mar 13, 2019 3:15:43 PM
 */
public class basicConnection {

	public static Connection connection = JDBCUtil.getConnection();
}
```

3. 小哥就简写一下`DAO`层类,便于举例: 增加数据表信息的类 + 删除数据表信息的类 + ······
```java
/**
 * @ClassName: basicConnectionTest
 * @Description: Insert the specified data into the database.
 * @author: Huang Yuhui
 * @date: Mar 16, 2019 10:40:11 PM
 */
public class InsertTest {

	Connection connection = basicConnection.connection;

	@Test
	public void insertInfoTest() {

		System.out.println("insertInfoTest : " + connection);
	}
}

```

```java
/**
 * @ClassName: DeleteTest
 * @Description: Delete the specified data from the database.
 * @author: Huang Yuhui
 * @date: Mar 16, 2019 11:05:44 PM
 */
public class DeleteTest {

	Connection connection2 = basicConnection.connection;

	@Test
	public void deleteInfoTest() {

		System.out.println("deleteInfoTest : " + connection2);
	}
}
```

4. 利用`JUnit`测试套件测试`Data acess object`中所有的测试类.
```java
/**
 * @ClassName: AllTest
 * @Description: Test all class.
 * @author: Huang Yuhui
 * @date: Mar 16, 2019 11:04:58 PM
 * 
 */
@RunWith(Suite.class)
@SuiteClasses({ InsertTest.class, DeleteTest.class })
public class AllTest {

}
```

1. 输出结果如下所示.由于`Connection`对象被抽取且被`static`修饰为了静态对象.继而在整个`DAO`中可以共享这个`连接数据库的对象`.而不必每一个类中都实例化一次`连接数据库的对象(Connection)`继而提升程序的性能（づ￣3￣）づ╭❤～
```java
insertInfoTest : com.mysql.cj.jdbc.ConnectionImpl@158a8276
deleteInfoTest : com.mysql.cj.jdbc.ConnectionImpl@158a8276
```

- *哎哟,不知觉又半夜啦! 晚安,好梦. remember love yourself deeply.*
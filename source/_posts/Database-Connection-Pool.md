---
title: Database Connection Pool
date: 2019-05-28 20:57:13
tags: [Java,JDBC]
---

## 学习笔记 : 数据库连接池
*简介 : 在JDBC编程中,每次创建和断开`Connection`对象都会消耗一定的时间和IO资源.为了避免频繁地创建数据库连接,工程师们提出了`数据库连接池`技术,其负责分配,管理和释放数据库连接,它允许应用程序重复使用现有的数据库连接,而不是重新建立连接 !*

### DataSource 接口
*简介 : 为了获取数据库连接对象(Connection),JDBC提供了`javax.sql.DataSource`接口,它负责与数据库建立连接,并定义了返回值为Connection对象的方法. 人们习惯性地把实现了该接口的类称为`数据源`,在数据源中存储了所有建立数据库连接的信息.*

- *`数据源中包含数据库库连接池,如果数据是水,数据库就是水库,数据源就是连接水库的管道,终端用户看到的数据集是管道里流出来的水.`*



### DBCP 数据源
*简介 : `DBCP`是数据库连接池(DataBase Connection Pool)的简称,是Apache组织下的开源连接池实现.其jar包中包含两个核心的类,分别是`BasicDataSourceFactory`和`BasicDataSource`.*
1. *BasicDataSource : 为DataSource的实现类,主要包含设置数据源对象的方法.*
2. *BasicDataSourceFactory : 为创建BasicDataSource对象的工厂类,它主要包含一个返回值为BasicDataSource对象的方法`createDataSource()`,该方法通过读取配置文件的信息生成数据源对象并返回给调用者.*

#### 使用BasicDataSource类创建数据源对象
1. *示例程序如下*
```java
package pers.huangyuhui.dbcp.test;

import java.sql.Connection;
import java.sql.DatabaseMetaData;
import java.sql.SQLException;

import javax.sql.DataSource;

import org.apache.commons.dbcp2.BasicDataSource;

/**
 * @ClassName: DBCPTest
 * @Description: 通过BasicDataSource类直接创建数据源对象
 * @author: HuangYuhui
 * @date: May 29, 2019 9:25:36 PM
 *
 */
public class DBCPTest {

	private static DataSource dataSource = null;

	static {
		// 获取DBCP数据源实现类对象
		BasicDataSource basicDataSource = new BasicDataSource();

		// 设置连接数据库需要的配置信息
		basicDataSource.setDriverClassName("com.mysql.cj.jdbc.Driver");
		basicDataSource.setUrl("jdbc:mysql://localhost/MyBatis?useSSL=false&serverTimezone=UTC&allowPublicKeyRetrieval=true");
		basicDataSource.setUsername("xxxxxx");
		basicDataSource.setPassword("xxxxxx");

		// 设置连接池的参数
		basicDataSource.setInitialSize(5);

		dataSource = basicDataSource;
	}

	public static void main(String[] args) throws SQLException {
		// 获取数据库连接对象
		Connection connection = dataSource.getConnection();
		// 获取数据库连接信息
		DatabaseMetaData databaseMetaData = connection.getMetaData();
		// 打印数据库连接信息
		System.out.println("URL: " + databaseMetaData.getURL() + "\nUser name: " + databaseMetaData.getUserName()
				+ "\nDirver name: " + databaseMetaData.getDriverName() + "\n");
	}
}
```

2. *程序运行结果如下*
```
URL : jdbc:mysql://localhost/MyBatis?useSSL=false&serverTimezone=UTC&allowPublicKeyRetrieval=true
User name : xxxxxx@localhost
Dirver name : MySQL Connector/J
```


#### 通过读取配置文件创建数据源对象
1. *db.properties : 数据源文件*
```t
#database configuration information
driverClassName=com.mysql.cj.jdbc.Driver
url=jdbc:mysql://LOCALHOST/MyBatis?useSSL=false&serverTimezone=UTC&allowPublicKeyRetrieval=true
username=xxxxxx
password=xxxxxx
#DBCP configuration information
initialSize=5
maxIdle=10
```
   
2. *DBCPTest2.java : 测试类*
```java
package pers.huangyuhui.dbcp.test;

import java.io.IOException;
import java.io.InputStream;
import java.sql.Connection;
import java.sql.DatabaseMetaData;
import java.sql.SQLException;
import java.util.Properties;

import javax.sql.DataSource;

import org.apache.commons.dbcp2.BasicDataSourceFactory;

/**
 * @ClassName: DBCPTest2
 * @Description: 通过读取配置文件创建数据源对象
 * @author: HuangYuhui
 * @date: May 29, 2019 9:45:15 PM
 *
 */
public class DBCPTest2 {

	public static DataSource dataSource = null;
	static {
		// 新建一个配置文件对象
		Properties properties = new Properties();

		// 通过类加载器找到文件路径并读取配置文件
		InputStream inputStream = new DBCPTest2().getClass().getClassLoader().getResourceAsStream("db.properties");
		try {
			// 把文件以输入流的形式加载到配置对象中
			properties.load(inputStream);
			// 创建数据源对象
			dataSource = BasicDataSourceFactory.createDataSource(properties);
		} catch (IOException e) {
			e.printStackTrace();
		} catch (Exception e) {
			e.printStackTrace();
		}

	}

	public static void main(String[] args) throws SQLException {
		// 获取数据库连接对象
		Connection connection = dataSource.getConnection();
		// 获取数据库连接信息
		DatabaseMetaData databaseMetaData = connection.getMetaData();
		// 打印数据库连接信息
		System.out.println("URL: " + databaseMetaData.getURL() + "\nUser name: " + databaseMetaData.getUserName()
				+ "\nDirver name: " + databaseMetaData.getDriverName() + "\nDriver version: "
				+ databaseMetaData.getDriverVersion());
	}

}
```

2. *程序运行结果如下*
```
URL : jdbc:mysql://LOCALHOST/MyBatis?useSSL=false&serverTimezone=UTC&allowPublicKeyRetrieval=true
User name : xxxxxx@localhost
Dirver name : MySQL Connector/J
Driver version : mysql-connector-java-8.0.11 (Revision: 6d4eaa273bc181b4cf1c8ad0821a2227f116fedf)
```



### C3P0 数据源
*简介 : `C3P0`实现了DataSource数据源接口,支持JDBC2,JDBC3的标准规范,易于扩展并且性能优越,著名的开源框架`Hibernate`和`Spring`使用的都是该数据源. C3P0中DataSource接口的实现类为`ComboPooledDataSource`,它是C3P0的核心类.*

#### 使用ComboPooledDataSource类创建数据库源对象
1. *示例程序如下*
```java
package pers.huangyuhui.c3p0.test;

import java.beans.PropertyVetoException;
import java.sql.Connection;
import java.sql.DatabaseMetaData;
import java.sql.SQLException;

import javax.sql.DataSource;

import com.mchange.v2.c3p0.ComboPooledDataSource;

/**
 * @ClassName: ComboPooledDataSourceTest
 * @Description: 通过ComboPooledDataSource类直接创建数据源对象
 * @author: HuangYuhui
 * @date: May 29, 2019 10:04:46 PM
 *
 */
public class ComboPooledDataSourceTest {

	private static DataSource dataSource = null;

	// 初始化c3p0数据源
	static {
		ComboPooledDataSource comboPooledDataSource = new ComboPooledDataSource();
		try {
			comboPooledDataSource.setDriverClass("com.mysql.cj.jdbc.Driver");
		} catch (PropertyVetoException e) {
			e.printStackTrace();
		}
		comboPooledDataSource.setJdbcUrl(
				"jdbc:mysql://localhost/MyBatis?useSSL=false&serverTimezone=UTC&allowPublicKeyRetrieval=true");
		comboPooledDataSource.setUser("xxxxxx");
		comboPooledDataSource.setPassword("xxxxxx");
		// 设置连接池的参数
		comboPooledDataSource.setInitialPoolSize(5);
		comboPooledDataSource.setMaxPoolSize(15);

		dataSource = comboPooledDataSource;
	}

	public static void main(String[] args) throws SQLException {

		// 获取数据库连接对象
		Connection connection = dataSource.getConnection();
		// 获取数据库连接信息
		DatabaseMetaData databaseMetaData = connection.getMetaData();
		// 输出数据库连接信息
		System.out.println("URL: " + databaseMetaData.getURL() + "\nUser name: " + databaseMetaData.getUserName()
				+ "\nDirver name: " + databaseMetaData.getDriverName() + "\nDriver version: "
				+ databaseMetaData.getDriverVersion());

	}
}
```

2. *程序运行结果如下*
```
URL : jdbc:mysql://localhost/MyBatis?useSSL=false&serverTimezone=UTC&allowPublicKeyRetrieval=true
User name : xxxxxx@localhost
Dirver name : MySQL Connector/J
Driver version : mysql-connector-java-8.0.11 (Revision: 6d4eaa273bc181b4cf1c8ad0821a2227f116fedf)
```


#### 通过配置文件创建数据源对象
1. *c3p0-config.xml : 数据源配置文件*
```xml
<?xml version="1.0" encoding="UTF-8"?>
<c3p0-config>
	<!-- 默认配置:在没有指定配置时默认使用该配置创建c3p0数据源对象 -->
	<default-config>
		<property name="user">root</property>
		<property name="password">GoodTime</property>
		<property name="driverClass">com.mysql.cj.jdbc.Driver</property>
		<property name="jdbcUrl">
			jdbc:mysql://LOCALHOST/MyBatis?useSSL=false&amp;serverTimezone=UTC&amp;allowPublicKeyRetrieval=true
		</property>
		<property name="checkoutTimeout">30000</property>
		<property name="initialPoolSize">10</property>
		<property name="maxIdleTime">100</property>
		<property name="maxPoolSize">100</property>
		<property name="minPoolSize">10</property>
		<property name="maxStatements">200</property>
	</default-config>

	<!-- 自定义配置 -->
	<named-config name="yu">
		<property name="user">root</property>
		<property name="password">GoodTime</property>
		<property name="driverClass">com.mysql.cj.jdbc.Driver</property>
		<property name="jdbcUrl">
			jdbc:mysql://LOCALHOST/MyBatis?useSSL=false&amp;serverTimezone=UTC&amp;allowPublicKeyRetrieval=true
		</property>
		<property name="initiaPoolSize">5</property>
		<property name="maxPoolSize">15</property>
	</named-config>
</c3p0-config>
```

2. *ComboPooledDataSourceTest2.java : 测试类*
```java
package pers.huangyuhui.c3p0.test;

import java.sql.Connection;
import java.sql.DatabaseMetaData;
import java.sql.SQLException;

import javax.sql.DataSource;

import com.mchange.v2.c3p0.ComboPooledDataSource;

public class ComboPooledDataSourceTest2 {

	public static DataSource dataSource = null;

	// 初始化C3P0数据源
	static {
		// 使用c3p0-config.xml配置文件中的named-config节点中name属性的值
		ComboPooledDataSource comboPooledDataSource = new ComboPooledDataSource("yu");
		dataSource = comboPooledDataSource;
	}

	public static void main(String[] args) throws SQLException {

		// 获取数据库对象
		Connection connection = dataSource.getConnection();
		// 获取数据库连接信息
		DatabaseMetaData databaseMetaData = connection.getMetaData();
		// 输出数据库配置信息
		System.out.println("URL: " + databaseMetaData.getURL() + "\nUser name: " + databaseMetaData.getUserName()
				+ "\nDirver name: " + databaseMetaData.getDriverName() + "\nDriver version: "
				+ databaseMetaData.getDriverVersion());

	}
}
```

3. *程序运行结果*
```
URL : jdbc:mysql://LOCALHOST/MyBatis?useSSL=false&serverTimezone=UTC&allowPublicKeyRetrieval=true
User name : xxxxxx@localhost
Dirver name : MySQL Connector/J
Driver version : mysql-connector-java-8.0.11 (Revision: 6d4eaa273bc181b4cf1c8ad0821a2227f116fedf)
```
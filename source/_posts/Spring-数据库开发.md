---
title: 'Spring:数据库开发'
date: 2019-05-16 15:00:49
tags: [Spring]
---

## 学习笔记 : Spring 数据库开发

### Spring JDBC
*简介 : Spring框架降低了Java EE API的使用难度,例如JDBC. Spring的JDBC模块负责数据库资源管理和错误处理,简化了开发人员对数据库的操作,从而将更多的精力投入到编写业务逻辑中.*


#### Spring JdbcTemplate 的解析
*针对数据库操作,Spring框架提供了`JdbcTemplate`类,该类是Spring框架数据抽象层的基础,Spring JDBC的核心类. 它继承自抽象类`JdbcAccessor`,同时实现了`JdbcOperations`接口.*

1. JdbcAccessor : 该类为子类提供了一些访问是数据库时使用的公共属性.
2. JdbcOperations : 该接口定义了在`JdbcTemplate`类中可以使用的操作集合,包括增删改查等操作.


#### Spring JDBC 的配置
*Spring JDBC模块主要由4个包组成,如下所示哟 ~*

1. `core`(核心包) : 包含了JDBC的核心功能,包括JdbcTemplate,SimpleJdbcInsert,SimpleJdbcCall类,以及NamedParameterJdbcTemplate类.
2. `dataSource`(数据源包) : 访问数据源的实用工具类,它有多种数据源的实现,可以在Java EE容器外部测试JDBC代码.
3. `object`(对象包) : 以面向对象的方式访问数据库,它允许执行查询并将返回结果作为业务对象,可以在数据表的列和业务对象的属性之间映射查询结果.
4. `support`(支持包) : 包含了core和object包的支持类,例如: 提供异常转换功能的SQLException类.

*由此可知,Spring对数据库的操作都封装在这几个包中,Spring JDBC的配置是在配置文件`applicationContext.xml`中完成的哟 ~ 其模板如下所示 :*

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans 
	http://www.springframework.org/schema/beans/spring-beans.xsd">

	<!-- 1: 配置数据源 -->
	<bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
		<!-- 数据库驱动 -->
		<property name="driverClassName" value="com.mysql.cj.jdbc.Driver" />
		<!-- 连接数据库的url -->
		<property name="url"
			value="jdbc:mysql://localhost:3306/xxxxxx"/>
		<!-- 连接数据库的用户名 -->
		<property name="username" value="xxxxxx" />
		<!-- 连接数据库的密码 -->
		<property name="password" value="xxxxxx" />
	</bean>

	<!-- 2: 配置JDBC模板 -->
	<bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
		<!-- 默认必须使用数据源 -->
		<property name="dataSource" ref="dataSource" />
	</bean>

	<!-- 3: 配置注入类 -->
	<bean id="xxxxxx" class="xxxxxx">
		<property name="jdbcTemplate" ref="jdbcTemplate" />
	</bean>

    ······
</beans>
```
*定义JdbcTemplate时,需要将dataSource注入到JdbcTemplate中,而其它需要使用JdbcTemplate的Bean,也需要将JdbcTemplate注入到该Bean中(`通常注入到Dao类中,在Dao类中进行与数据库的相关操作`).*



### Spring JdbcTempalte 的常用方法
*`JdbcTemplate`类中提供了大量的操作数据库的方法,下面通过一个简单的`CURE`来体现一下Spring JDBC代码的简洁美 ~*

1. *applicationContext.xml : 配置文件*
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans 
	http://www.springframework.org/schema/beans/spring-beans.xsd">

	<!-- 1: 配置数据源 -->
	<bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
		<!-- 数据库驱动 -->
		<property name="driverClassName" value="com.mysql.cj.jdbc.Driver" />
		<!-- 连接数据库的url -->
		<property name="url" value="jdbc:mysql://localhost/Spring?useSSL=false&amp;serverTimezone=UTC&amp;allowPublicKeyRetrieval=true" />
		<!-- 连接数据库的用户名 -->
		<property name="username" value="xxxxxx" />
		<!-- 连接数据库的密码 -->
		<property name="password" value="xxxxxx" />
	</bean>

	<!-- 2: 配置JDBC模板 -->
	<bean id="jdbcTemplate"
		class="org.springframework.jdbc.core.JdbcTemplate">
		<!-- 默认必须使用数据源 -->
		<property name="dataSource" ref="dataSource" />
	</bean>

	<!-- 3: 配置注入类 -->
	<bean id="accountDao" class="pers.huangyuhui.spring.jdbc.dao.impl.AccountDaoImpl">
		<!-- 将jbdcTemplate注入到accountDao实例中 -->
		<property name="jdbcTemplate" ref="jdbcTemplate" />
	</bean>

</beans>
```

2. *Account.java : 封装用户账户信息*
```java
package pers.huangyuhui.spring.jdbc.bean;

/**
 * @ClassName: Account
 * @Description: 用户账户信息实体表
 * @author: HuangYuhui
 * @date: May 17, 2019 11:56:41 AM
 *
 */
public class Account {

	private Integer id;
	private String username;
	private Double balance;

	public Integer getId() {
		return id;
	}

	public void setId(Integer id) {
		this.id = id;
	}

	public String getUsername() {
		return username;
	}

	public void setUsername(String username) {
		this.username = username;
	}

	public Double getBalance() {
		return balance;
	}

	public void setBalance(Double balance) {
		this.balance = balance;
	}

	@Override
	public String toString() {
		return "Account [id=" + id + ", username=" + username + ", balance=" + balance + "]";
	}

}
```

3. *AccountDao.java : 操作账户信息的接口*
```java
package pers.huangyuhui.spring.jdbc.dao;

import java.util.List;

import pers.huangyuhui.spring.jdbc.bean.Account;

/**
 * @ClassName: AccountDao
 * @Description: 操作用户账户表信息
 * @author: HuangYuhui
 * @date: May 17, 2019 12:00:43 PM
 *
 */
public interface AccountDao {

	/**
	 * @Title: createTable
	 * @Description: 创建数据表
	 * @param: sql
	 * @return: void
	 */
	public void createTable(String sql);

	/**
	 * @Title: findAccountById
	 * @Description: 查找表数据
	 * @param: id
	 * @return: Account
	 */
	public Account findAccountById(int id);

	/**
	 * @Title: findAllAccount
	 * @Description: 查找全部表数据
	 * @return: List<Account>
	 */
	public List<Account> findAllAccount();

	/**
	 * @Title: addAccount
	 * @Description: 添加表数据
	 * @param: account
	 * @return: int
	 */
	public int addAccount(Account account);

	/**
	 * @Title: updateAccount
	 * @Description: 更新表数据
	 * @param: account
	 * @return: int
	 */
	public int updateAccount(Account account);

	/**
	 * @Title: deleteAccount
	 * @Description: 删除表数据
	 * @param: id
	 * @return: int
	 */
	public int deleteAccount(int id);
}
```

4. *AccountDaoImpl.java : AccountDao的实现类*
```java
package pers.huangyuhui.spring.jdbc.dao.impl;

import java.util.List;

import org.springframework.jdbc.core.BeanPropertyRowMapper;
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.jdbc.core.RowMapper;

import pers.huangyuhui.spring.jdbc.bean.Account;
import pers.huangyuhui.spring.jdbc.dao.AccountDao;

/**
 * @ClassName: AccountDaoImpl
 * @Description: AccountDao接口的实现类
 * @author: HuangYuhui
 * @date: May 17, 2019 12:05:34 PM
 *
 */
public class AccountDaoImpl implements AccountDao {

	// 声明JdbcTemplate属性及其setter方法
	private JdbcTemplate jdbcTemplate;

	// 获取JdbcTemplate实例
	public void setJdbcTemplate(JdbcTemplate jdbcTemplate) {
		this.jdbcTemplate = jdbcTemplate;
	}

	@Override
	public void createTable(String sql) {

		jdbcTemplate.execute(sql);
	}

	@Override
	public int addAccount(Account account) {
		String sql = "insert into account(username,balance) value(?,?)";

		// 定义数组来存储SQL语句中的参数. Good idea ~
		Object[] objects = new Object[] { account.getUsername(), account.getBalance() };

		// 执行添加操作,返回受SQL语句影响的条数
		return jdbcTemplate.update(sql, objects);
	}

	@Override
	public int updateAccount(Account account) {
		String sql = "update account set username=? , balance=?  where id = ?";

		// 注意: `?`需与设置的参数顺序对应哟 !
		Object[] objects = new Object[] { account.getUsername(), account.getBalance(), account.getId() };

		return this.jdbcTemplate.update(sql, objects);
	}

	@Override
	public int deleteAccount(int id) {
		String sql = "delete from account where id = ?";

		return this.jdbcTemplate.update(sql, id);
	}

	@Override
	public Account findAccountById(int id) {
		String sql = "select id,username,balance from account where id = ?";

		// 创建BeanPropertyRowMapper对象
		// 它可以自动地将数据表中的数据映射到用户自定义的类中(前提是:用户自定义类中的字段要与数据表中的字段相对应)
		RowMapper<Account> rowMapper = new BeanPropertyRowMapper<Account>(Account.class);

		// 将id绑定到SQL语句中,并通过RowMapper返回一个Object类型的单行记录
		return this.jdbcTemplate.queryForObject(sql, rowMapper, id);
	}

	@Override
	public List<Account> findAllAccount() {
		String sql = "select id,username,balance from account";

		RowMapper<Account> rowMapper = new BeanPropertyRowMapper<Account>(Account.class);

		// 执行静态的SQL查询,并通过RowMapper返回结果集
		return this.jdbcTemplate.query(sql, rowMapper);

	}

}
```

5.*CURETest.java : 测试类*
```java
package pers.huangyuhui.spring.jdbc.test;

import java.util.List;

import org.junit.BeforeClass;
import org.junit.Ignore;
import org.junit.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

import pers.huangyuhui.spring.jdbc.bean.Account;
import pers.huangyuhui.spring.jdbc.dao.AccountDao;

/**
 * @ClassName: CURDTest
 * @Description: 测试Spring JDBC的增删改查功能
 * @author: HuangYuhui
 * @date: May 17, 2019 12:26:43 PM
 *
 */
public class CURDTest {

	private static Account account;
	private static AccountDao accountDao;
	private static ApplicationContext applicationContext;

	@BeforeClass
	public static void init() {
		account = new Account();
		// 加载配置文件
		applicationContext = new ClassPathXmlApplicationContext("applicationContext.xml");
		// 获取AccountDao实例
		accountDao = (AccountDao) applicationContext.getBean("accountDao");
	}

	@Test
	// 创建用户账户信息数据表
	public void createTableTest() {
		String sql = 
				"create table account\r\n" + 
				"(\r\n" + 
				"	id int primary key auto_increment,\r\n" + 
				"	username varchar(10) not null,\r\n" + 
				"	balance double not null\r\n" + 
				")";

		accountDao.createTable(sql);
		System.out.println("success to create the table of account ~");
	}

	@Ignore
	@Test
	// 添加账户信息
	public void addAccountTest() {

		// 向Account对象中添加数据
		account.setUsername("YUbuntu0109");
		account.setBalance(666666.0);

		// 获取添加操作返回的结果
		int num = accountDao.addAccount(account);
		if (num > 0) {
			System.out.println("成功添加了 " + num + " 条数据哟 ~");
		} else {
			System.out.println("添加数据失败 !");
		}

	}

	@Ignore
	@Test
	// 更新指定账户信息
	public void updateAccountTest() {

		account.setId(3);
		account.setUsername("update");
		account.setBalance(999999.0);

		int num = accountDao.updateAccount(account);
		if (num > 0) {
			System.out.println("更新成功了 " + num + " 条数据哟 ~");
		} else {
			System.out.println("更新数据失败 ! ");
		}

	}

	@Ignore
	@Test
	// 删除指定账户信息
	public void deleteAccountTest() {

		int num = accountDao.deleteAccount(1);
		if (num > 0) {
			System.out.println("删除了 " + num + " 条数据哟 ~");
		} else {
			System.out.println("删除数据失败 !");
		}
	}

	@Ignore
	@Test
	// 查询指定账户信息
	public void findAccountByIdTest() {
		account = accountDao.findAccountById(2);

		System.out.println(account);
	}

	@Ignore
	@Test
	// 查询所有账户信息
	public void findAllAccount() {
		List<Account> accountInfo = accountDao.findAllAccount();

		System.out.println(accountInfo);

	}

}
```



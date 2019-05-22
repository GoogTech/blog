---
title: 'MyBatis:动态SQL'
date: 2019-05-16 15:01:11
tags: [MyBatis]
---

## 学习笔记 : MyBatis之动态SQL
*简介 : MyBatis提供的对SQL语句动态组装的功能解决了开发人员在使用JDBC或其他的框架进行数据库开发时,需要手动拼装SQL的繁琐问题.*

### 动态SQL元素
*动态SQL是MyBatis强大特性之一,`MyBatis 3`采用了功能强大的基于`OGNL`的表达式来完成动态SQL,其主要元素如下.*
1. `<if>` : 判断语句,用于单条分支判断.
2. `<choose>(<when>,<otherwise>)` : 相当于Java中的swith...case...default语句,用于多条件分支判断.
3. `<where>,<trim>,<set>` : 辅助元元素,用于处理一些SQL拼装,特殊字符问题.
4. `<foreach>` : 循环语句,常用于in语句等列举条件中.
5. `<bind>` : 从`OGNL`表达式中创建一个变量,并将其绑定到上下文,常用于模糊查询的sql中. 

- *扩展 : `OGNL`是Object-Graph Navigation Language的缩写,它是一种功能强大的表达式语言,通过它简单一致的表达式语法,可以存取对象的任意属性,调用对象的方法,遍历整个对象的结构图,实现字段类型转化等功能.它使用相同的表达式去存取对象的属性.这样可以更好的取得数据.*



### 应用案例
*下面通过一个操作客户信息的综合案例来学习SQL动态元素 (っ•̀ω•́)っ✎⁾⁾ ~*

1. *客户信息表结构*
```sql
create table t_customer (

	id INT(32) PRIMARY KEY AUTO_INCREMENT,
	username VARCHAR(20) NOT NULL,
	jobs VARCHAR(45) NOT NULL,
	phone VARCHAR(16) NOT NULL
	
)
```

2. *db.properties : 数据库配置文件*
```t
#database configuration information
jdbc.driver=com.mysql.cj.jdbc.Driver
jdbc.url=jdbc:mysql://LOCALHOST/MyBatis?useSSL=false&serverTimezone=UTC&allowPublicKeyRetrieval=true
jdbc.username=root
jdbc.password=GoodTime
```

3. *mybatis-config.xml : MyBatis核心配置文件*
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
 PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
 "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
	<!-- 数据库配置文件 -->
	<properties resource="db.properties" />
	<!-- 配置环境.默认环境id为MySQL -->
	<environments default="MySQL">
		<environment id="MySQL">
			<!-- 使用JDBC事务管理 -->
			<transactionManager type="JDBC" />
			<!-- 数据库连接池 -->
			<dataSource type="POOLED">
				<property name="driver" value="${jdbc.driver}" />
				<property name="url" value="${jdbc.url}" />
				<property name="username" value="${jdbc.username}" />
				<property name="password" value="${jdbc.password}" />
			</dataSource>
		</environment>
	</environments>
	<!-- 将SQL映射文件注册到全局配置文件中 -->
	<mappers>
		<mapper resource="pers/huangyuhui/mybatis/mapper/CustomerMapper.xml" />
	</mappers>
</configuration>
```

4. *Customer.java : 封装客户信息*
```java
package pers.huangyuhui.mybatis.bean;

/**
 * @ClassName: Customer
 * @Description: 客户信息实体表
 * @author: HuangYuhui
 * @date: May 18, 2019 11:37:59 AM
 *
 */
public class Customer {

	private Integer id;
	private String username;
	private String job;
	private String phone;

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

	public String getJob() {
		return job;
	}

	public void setJob(String job) {
		this.job = job;
	}

	public String getPhone() {
		return phone;
	}

	public void setPhone(String phone) {
		this.phone = phone;
	}

	@Override
	public String toString() {
		return "Customer [id=" + id + ", username=" + username + ", job=" + job + ", phone=" + phone + "]";
	}

}
```

5. *CustomerMapper.xml : 操作't_customer'数据表的映射文件*
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" 
"http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="pers.huangyuhui.mybatis.mapper.CustomerMapper">

	<!-- (<if>)将客户名和职业组合作为查询客户信息列表的条件 -->
	<select id="findCustomerByNameAndJob" parameterType="pers.huangyuhui.mybatis.bean.Customer" 
				resultType="pers.huangyuhui.mybatis.bean.Customer">
		select id,username,job,phone from t_customer where 1=1
		<if test="username!=null and username!=''">
			and username like concat('%',#{username},'%')
		</if>
		<if test="job!=null and job!=''">
			and job=#{job}
		</if>
	</select>
	
	<!-- (<where>+<if>)将客户名和职业组合作为查询客户信息列表的条件 -->
	<select id="findCustomerByNameAndJob_where" parameterType="pers.huangyuhui.mybatis.bean.Customer" 
				resultType="pers.huangyuhui.mybatis.bean.Customer">
		select id,username,job,phone from t_customer
		<where>
			<if test="username!=null and username!=''">
				and username like concat('%',#{username},'%')
			</if>
			<if test="job!=null and job!=''">
				and job=#{job}
			</if>
		</where>
	</select>
	
	<!-- (<trim>+<if>)将客户名和职业组合作为查询客户信息列表的条件 -->
	<select id="findCustomerByNameAndJob_trim" parameterType="pers.huangyuhui.mybatis.bean.Customer" 
				resultType="pers.huangyuhui.mybatis.bean.Customer">
		select id,username,job,phone from t_customer
		<trim prefix="where" prefixOverrides="and">
			<if test="username!=null and username!=''">
				and username like concat('%',#{username},'%')
			</if>
			<if test="job!=null and job!=''">
				and job=#{job}
			</if>
		</trim>
	</select>
	
	<!-- (<where>+<when>+<otherwise>)将客户名或职业其中一项作为查询客户信息的条件 -->
	<select id="findCustomerByNameOrJob" parameterType="pers.huangyuhui.mybatis.bean.Customer" 
				resultType="pers.huangyuhui.mybatis.bean.Customer">
		select id,username,job,phone from t_customer
		<where>
			<choose>
				<when test="username!=null and username!=''">
					and username like concat('%',#{username},'%')
				</when>
				<when test="job!=null and job!=''">
					and job = #{job}
				</when>
				<otherwise>
					and phone is not null
				</otherwise>
			</choose>	
		</where>
	</select>
	
	<!-- (<set>+<if>)根据id更新客户信息 -->
	<update id="updateCustomer" parameterType="pers.huangyuhui.mybatis.bean.Customer">
		update t_customer
		<set>
			<if test="username !=null and username !=''">
				username=#{username}
			</if>
			<if test="job !=null and job !=''">
				job=#{job}
			</if>
			<if test="phone !=null and phone !=''">
				phone=#{phone}
			</if>
		</set>
		where id = #{id}
	</update>
	
	<!-- (<foreach>)根据客户id批量查询客户信息 -->
	<select id="findCustomerByIds" parameterType="List" resultType="pers.huangyuhui.mybatis.bean.Customer">
		select id,username,job,phone from t_customer where id in
		<foreach item="id" index="index" collection="list" open="(" separator="," close=")">
			#{id}
		</foreach>
	</select>
		
	<!-- (<bind>)替换${},防止SQL注入问题.根据客户名模糊查询客户信息 -->	
	<select id="findCustomerByName" parameterType="pers.huangyuhui.mybatis.bean.Customer" 
				resultType="pers.huangyuhui.mybatis.bean.Customer">
		<bind name="pattern_username" value=" '%'  + _parameter.getUsername + '%' "/>
		select id,username,job,phone from t_customer where username like #{pattern_username}
	</select>
</mapper>
```

6. *MyBatisUtils.java : MyBatis工具类*
```java
package pers.huangyuhui.mybatis.util;

import java.io.IOException;
import java.io.Reader;

import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;

/**
 * @ClassName: MyBatisUtils
 * @Description: MyBatis工具类
 * @author: HuangYuhui
 * @date: May 18, 2019 11:43:31 AM
 *
 */
public class MyBatisUtils {

	private MyBatisUtils() {

	}

	private static SqlSessionFactory sqlSessionFactory = null;

	static {
		// 使用MyBatis提供的Resources类加载MyBatis的配置文件
		try (Reader reader = Resources.getResourceAsReader("mybatis-config.xml");) {
			// 构建SqlSessionFactory工厂
			sqlSessionFactory = new SqlSessionFactoryBuilder().build(reader);
		} catch (IOException e) {
			e.printStackTrace();
		}
	}

	// 获取SqlSession对象的静态方法
	public static SqlSession getSession() {
		return sqlSessionFactory.openSession();
	}
}
```

7. *MyBatisTest.java : 测试类*
```java
package pers.huangyuhui.mybatis.test;

import java.io.IOException;
import java.util.ArrayList;
import java.util.List;

import org.apache.ibatis.session.SqlSession;
import org.junit.BeforeClass;
import org.junit.Ignore;
import org.junit.Test;

import pers.huangyuhui.mybatis.bean.Customer;
import pers.huangyuhui.mybatis.util.MyBatisUtils;

/**
 * @ClassName: MyBatisTest
 * @Description: 测试
 * @author: HuangYuhui
 * @date: May 18, 2019 8:41:39 AM
 *
 */
public class MyBatisTest {

	private static Customer customer;
	private static String namespace = "pers.huangyuhui.mybatis.mapper.CustomerMapper.";

	@BeforeClass
	public static void init() throws IOException {
		customer = new Customer();
	}

	@Test
	@Ignore
	// 将客户名和职业组合作为查询客户信息列表的条件
	public void findCustomerByNameAndJobTest() {
		// 获取SqlSession对象
		SqlSession sqlSession = MyBatisUtils.getSession();

		// 封装需要组合查询的条件
		customer.setUsername("YUbuntu0109");
		customer.setJob("programmer");

		// 执行SqlSession的查询方法,并返回结果集
		List<Customer> customers = sqlSession.selectList(namespace + "findCustomerByNameAndJob", customer);

		// 输出查询结果信息
		for (Customer customer : customers) {
			System.out.println(customer);
		}

		// 关闭SqlSession
		sqlSession.close();

	}

	@Test
	@Ignore
	// 将客户名或职业其中一项作为查询客户信息的条件
	public void findCustomerByNameOrJobTest() {
		SqlSession sqlSession = MyBatisUtils.getSession();

		customer.setUsername("YUbuntu0109");
		customer.setJob("student");

		List<Customer> customers = sqlSession.selectList(namespace + "findCustomerByNameOrJob", customer);
		for (Customer customer : customers) {
			System.out.println(customer);
		}

		sqlSession.close();
	}

	@Test
	@Ignore
	// 根据客户id更新客户信息
	public void updateCustomerTest() {
		SqlSession sqlSession = MyBatisUtils.getSession();

		customer.setId(1);
		customer.setPhone("15711111111");

		int rows = sqlSession.update(namespace + "updateCustomer", customer);
		if (rows > 0) {
			System.out.println("成功更新了 " + rows + " 条数据哟 ~");
		} else {
			System.out.println("数据更新操作失败 !");
		}

		sqlSession.commit();
		sqlSession.close();

	}

	@Test
	@Ignore
	// 根据客户id批量查询客户信息
	public void findCustomerByIdsTest() {
		SqlSession sqlSession = MyBatisUtils.getSession();

		// 创建List集合,封装查询id
		List<Integer> ids = new ArrayList<Integer>();
		ids.add(1);
		ids.add(2);
		ids.add(3);

		// 查询id为1,2,3的客户信息
		List<Customer> customers = sqlSession.selectList(namespace + "findCustomerByIds", ids);
		for (Customer customer : customers) {
			System.out.println(customer);
		}

		sqlSession.close();

	}

	@Test
	@Ignore
	// 根据客户名模糊查询客户信息
	public void findCustomerByNameTest() {

		SqlSession sqlSession = MyBatisUtils.getSession();
		customer.setUsername("Y");

		List<Customer> customers = sqlSession.selectList(namespace + "findCustomerByName", customer);
		for (Customer customer : customers) {
			System.out.println(customer);
		}

		sqlSession.close();

	}

}
```

8. *log4j.properties : 日志配置文件*
```t
#Global configuration
log4j.rootLogger=DEBUG,stdout
#Log configuration
log4j.logger.pers.huangyuhui.mybatis.test=TRACE
#Console configuration
log4j.appender.stdout=org.apache.log4j.ConsoleAppender
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.layout.ConversionPattern=%5p [%t] - %m%n
```
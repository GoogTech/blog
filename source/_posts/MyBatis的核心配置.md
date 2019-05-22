---
title: MyBatis的核心配置
date: 2019-05-15 12:45:09
tags: [MyBatis]
---

## 学习笔记 : MyBatis的核心配置

### MyBatis的核心对象

#### SqlSessionFactory
*`SqlSessionFactory`是MyBatis中十分重要的对象,它是单个数据库映射关系经过编译后的内存镜像,其作用是创建`SqlSession`. SqlSessionFactory对象是线程安全的,它一旦被创建,在整个应用执行期间都会存在.如果我们多次地创建同一个数据库的SqlSessionFactory势必会耗尽数据库资源! 通常每一个数据库都会只对应一个SqlSessionFactory,所以在构建SqlSessionFactory时建议使用`单例模式`哟 !*

#### SqlSession
*`SqlSession`是MyBatis框架中另一个重要的对象,它是应用程序与持久层之间执行交互操作的一个单线程对象,其主要作用是执行持久化操作. 注意: 每一个线程都应该有一个自己的SqlSession实例,并且该实例是不能被共享的,同时`SqlSession`实例也是线程不安全的,因此其使用范围最好在一次请求或一个方法中,绝不能将其放在一个类的静态字段,实例或任何类型的管理范围中使用.使用后理应及时地关闭它 !*



### 配置文件

#### 主要元素结构图
![ ](MyBatis的核心配置/MyBatis配置文件中的主要元素.png)



### 映射文件
*官方简介 : MyBatis的真正强大在于它的映射语句,这是它的魔力所在.由于它的异常强大,映射器的XML文件就显得相对简单.如果拿它跟具有相同功能的JDBC代码进行对比,你会立即发现省掉了将近95%的代码! 俺喜欢简洁(✪ω✪) ~*

#### 主要元素
*在映射文件中,`<mapper>`元素是映射文件的根元素,其它元素都是它的子元素,其子元素及其作用如下所示 :*
1. `<select>` : 映射查询语句,可自定义参数,返回结果等.
2. `<insert>` : 映射插入语句,执行后返回一个整数,代表插入到条数.
3. `<update>` : 映射更新语句,执行后返回一个整数,代表更新的条数.
4. `<delete>` : 映射删除语句,执行后返回一个整数,代表删除的条数.
5. `<sql>` : 用于定义一部分SQL,然后可被其它语句引用此SQL.
6. `<cache-ref>` : 其他命名空间缓存配置的引用.
7. `<resultMap>` : 用于描述如何从数据库结果集中来加载数据.

#### 简单案例
*MyBatis框架的强大之处体现在映射文件,下面使用上述主要元素来编写一个简单的CURE程序 ~*

1. *数据表*
```sql
# 客户信息表(用于测试增删改查元素)
create table t_customer (

	id INT(32) PRIMARY KEY AUTO_INCREMENT,
	username VARCHAR(20) NOT NULL,
	job VARCHAR(45) NOT NULL,
	phone VARCHAR(16) NOT NULL
	
)

# 用户信息表(用于测试<resultMap>元素)
CREATE TABLE t_user(
	
	t_id INT PRIMARY KEY AUTO_INCREMENT,
	t_name VARCHAR(20) NOT NULL,
	t_age INT NOT NULL
)
```

2. *db.properties : 数据库配置信息*
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
		<mapper resource="pers/huangyuhui/mybatis/mapper/UserMapper.xml"/>
		<mapper resource="pers/huangyuhui/mybatis/mapper/CustomerMapper.xml"/>
	</mappers>
</configuration>
```

4. *CustomerMapper.xml : 操作't_customer'数据表的映射文件*
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" 
"http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="pers.huangyuhui.mybatis.mapper.CustomerMapper">

	<!-- 使用<sql>元素定义可重用的sql代码片段,简化代码 -->
	<sql id="customerColumns">id,username,job,phone</sql>

	<!-- 使用<insert>元素查询客户信息 -->
	<select id="findCustomerById" parameterType="Integer" resultType="pers.huangyuhui.mybatis.bean.Customer">
		select <include refid="customerColumns"/> from t_customer where id = #{id}
	</select>
	
	<!-- 使用<insert>元素添加客户信息并返回数据库生成的主键值 -->
	<insert id="addCustomer" parameterType="pers.huangyuhui.mybatis.bean.Customer" keyProperty="id" useGeneratedKeys="true">
		insert into t_customer(username,job,phone) values(#{username},#{job},#{phone})
	</insert>
	
	<!-- 使用<insert>元素添加客户信息并使用MyBatis提供的方式生成主键值 -->
	<insert id="addCustomer2" parameterType="pers.huangyuhui.mybatis.bean.Customer" >
		<selectKey keyProperty="id" resultType="Integer" order="BEFORE">
			select if(max(id) is null,1,max(id)+1) as newId from t_customer
		</selectKey>
		insert into t_customer(id,username,job,phone) values(#{id},#{username},#{job},#{phone})
	</insert>
	
	<!-- 使用<update>元素更新客户信息 -->
	<update id="updateCustomerById" parameterType="pers.huangyuhui.mybatis.bean.Customer">
		update t_customer set username=#{username},job=#{job},phone=#{phone} where id = #{id}
	</update>
	
	<!-- 使用<delete>元素删除客户信息 -->
	<delete id="deleteCustomerById" parameterType="Integer">
		delete from t_customer where id = #{id}
	</delete>
	
</mapper>
```

5. *UserMapper.xml : 操作't_user'数据表的映射文件*
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" 
"http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="pers.huangyuhui.mybatis.mapper.UserMapper">
	<!-- <resultMap>元素可解决数据表中的列与需要返回的对象的属性名可能不一致的问题	 -->
	<resultMap type="pers.huangyuhui.mybatis.bean.User" id="resultMap">
		<id property="id" column="t_id"/>
		<result property="name" column="t_name"/>
		<result property="age" column="t_age"/>
	</resultMap>
	<select id="findAllUser" resultMap="resultMap">
		select t_id,t_name,t_age from t_user
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
 * @date: May 18, 2019 3:59:18 PM
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
import java.util.List;

import org.apache.ibatis.session.SqlSession;
import org.junit.BeforeClass;
import org.junit.Ignore;
import org.junit.Test;

import pers.huangyuhui.mybatis.bean.Customer;
import pers.huangyuhui.mybatis.bean.User;
import pers.huangyuhui.mybatis.util.MyBatisUtils;

/**
 * @ClassName: MyBatisTest
 * @Description: MyBatis核心配置: CURE
 * @author: HuangYuhui
 * @date: May 18, 2019 4:03:47 PM
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
	// 根据客户编id查询客户信息
	public void findCustomerByIdTest() {

		// 获取SqlSession对象
		SqlSession sqlSession = MyBatisUtils.getSession();

		// 执行SqlSession的查询方法,并返回结果
		Customer customerInfo = sqlSession.selectOne(namespace + "findCustomerById", 1);

		// 输出查询结果信息
		System.out.println(customerInfo);

		// 关闭SqlSession
		sqlSession.close();

	}

	@Test
	@Ignore
	// 插入客户信息并获取由数据库生成的主键值
	public void addCustomerTest() {
		SqlSession sqlSession = MyBatisUtils.getSession();

		customer.setUsername("YUbuntu0109");
		customer.setJob("student ~");
		customer.setPhone("15111111111");

		int rows = sqlSession.insert(namespace + "addCustomer", customer);

		if (rows > 0) {
			System.out.println("插入数据的主键id值为: " + customer.getId());
			System.out.println("成功插入了 " + rows + " 条数据哟 ~");
		} else {
			System.out.println("插入数据操作失败 !");
		}

		sqlSession.commit();
		sqlSession.close();
	}

	@Test
	@Ignore
	// 添加客户信息并使用MyBatis提供的方式生成主键值
	public void addCustomer2Test() {
		SqlSession sqlSession = MyBatisUtils.getSession();

		customer.setUsername("YUbuntu0109");
		customer.setJob("student ~");
		customer.setPhone("13111111111");

		int rows = sqlSession.insert(namespace + "addCustomer2", customer);
		if (rows > 0) {
			System.out.println("mybatis生成的主键为: " + customer.getId());
			System.out.println("成功插入了 " + rows + " 条数据哟 ~");
		} else {
			System.out.println("插入数据操作失败 !");
		}

		sqlSession.commit();
		sqlSession.close();
	}

	@Test
	@Ignore
	// 根据客户id更新客户信息
	public void updateCustomerByIdTest() {
		SqlSession sqlSession = MyBatisUtils.getSession();

		customer.setId(1);
		customer.setUsername("YUbuntu0109");
		customer.setJob("speaker");

		int rows = sqlSession.update(namespace + "updateCustomerById", customer);
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
	// 根据客户id删除客户信息
	public void deleteCustomerById() {

		SqlSession sqlSession = MyBatisUtils.getSession();
		int rows = sqlSession.delete(namespace + "deleteCustomerById", 1);
		if (rows > 0) {
			System.out.println("成功删除了 " + rows + " 条记录哟 ~");
		} else {
			System.out.println("删除数据操作失败 !");
		}

		sqlSession.commit();
		sqlSession.close();

	}

	@Test
    @Ignore
	// 使用<resultMap>元素查找`t_user`表中所有用户信息
	public void findAllUserTest() {

		SqlSession sqlSession = MyBatisUtils.getSession();

		List<User> users = sqlSession.selectList("pers.huangyuhui.mybatis.mapper.UserMapper.findAllUser");
		for (User user : users) {
			System.out.println(user);
		}

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
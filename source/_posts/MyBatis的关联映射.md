---
title: MyBatis的关联映射
date: 2019-05-17 10:21:48
tags: [MyBatis]
---

## 学习笔记 : `MyBatis`关联映射
*简介 : 在实际的开发中,对数据库的操作常常会涉及多张表,这在面向对象中就涉及了对象与对象之间对的关联关系. 针对多表之间的操作,MyBatis提供了关联映射,通过关联映射就可以很好地处理对象与对象之间的关联关系.*

*`MyBatis`在映射文件中加载关联关系对象主要通过两种方式 :*
1. *`嵌套查询` : 指通过执行另一条SQL映射语句来返回预期的复杂类型.*
2. *`嵌套结果` : 使用嵌套结果映射来处理复杂的联合结果的子集.*



### 配置文件(为下面程序示例做准备)
1. *db.properties : 连接数据库的配置文件*
```t
#database configuration information
jdbc.driver=com.mysql.cj.jdbc.Driver
jdbc.url=jdbc:mysql://LOCALHOST/MyBatis?useSSL=false&serverTimezone=UTC&allowPublicKeyRetrieval=true
jdbc.username=xxxxxx
jdbc.password=xxxxxx
```

2. *mybatis-config.xml : MyBatis核心配置文件*
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <!-- 读取数据库配置文件 -->
    <properties resource="db.properties"/>

    <settings>
        <!-- 打开延迟加载的开关 -->
        <setting name="lazyLoadingEnabled" value="true"/>
        <!-- 将积极加载改为延迟加载,既按需加载 -->
        <setting name="aggressiveLazyLoading" value="false"/>
    </settings>

    <!-- 使用扫描包的形式定义别名 -->
    <typeAliases>
        <package name="pers.huangyuhui.mybatis.bean"/>
    </typeAliases>

    <!-- 配置环境.默认环境id为MySQL -->
    <environments default="MySQL">
        <environment id="MySQL">
            <!-- 使用JDBC事务管理 -->
            <transactionManager type="JDBC"/>
            <!-- 数据库连接池 -->
            <dataSource type="POOLED">
                <property name="driver" value="${jdbc.driver}"/>
                <property name="url" value="${jdbc.url}"/>
                <property name="username" value="${jdbc.username}"/>
                <property name="password" value="${jdbc.password}"/>
            </dataSource>
        </environment>
    </environments>
    <!-- 注册SQL映射文件 -->
    <mappers>
        <mapper resource="pers/huangyuhui/mybatis/mapper/IdCardMapper.xml"/>
        <mapper resource="pers/huangyuhui/mybatis/mapper/PersonMapper.xml"/>
        <mapper resource="pers/huangyuhui/mybatis/mapper/UserMapper.xml"/>
        <mapper resource="pers/huangyuhui/mybatis/mapper/OrderMapper.xml"/>
        <mapper resource="pers/huangyuhui/mybatis/mapper/ProductMapper.xml"/>
    </mappers>
</configuration>
```

3. *log4j.properties : 日志配置文件*
```t
#Global configuration
log4j.rootLogger=DEBUG,stdout
#Log configuration
log4j.logger.pers.huangyuhui.mybatis.test=DEBUG
#Console configuration
log4j.appender.stdout=org.apache.log4j.ConsoleAppender
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.layout.ConversionPattern=%5p [%t] - %m%n
```



### 一对一
*以人与身份证之间的一对一关联关系为例,为简化代码使用`嵌套结果`方式加载关联关系对象,程序示例如下 :*
1. *SQL 数据表文件*
```sql
-- auto-generated definition by Intellij IDEA
create table tb_idcard
(
    id   int auto_increment primary key,
    code varchar(18) null
);

create table tb_person
(
    id      int auto_increment primary key,
    name    varchar(10) null,
    age     int         null,
    sex     char(1)     null,
    card_id int         null,
    constraint tb_person_card_id_uindex unique (card_id),
    constraint tb_person_tb_idcard_id_fk foreign key (card_id) references tb_idcard (id)
);
```

1. *IdCard.java : 存储身份证信息, Person.java : 存储个人信息*
```java
package pers.huangyuhui.mybatis.bean;

/**
 * @project: mybatis_associated_mapping
 * @description: 身份证信息
 * @author: HuangYuhui
 * @date: 6/1/2019-8:28 AM
 * @version: 1.0
 */
public class IdCard {

    private Integer id;
    private String code;

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getCode() {
        return code;
    }

    public void setCode(String code) {
        this.code = code;
    }

    @Override
    public String toString() {
        return "IdCard{" + "id=" + id + ", code='" + code + '}';
    }
}


package pers.huangyuhui.mybatis.bean;

/**
 * @project: mybatis_associated_mapping
 * @description: 个人信息
 * @author: HuangYuhui
 * @date: 6/1/2019-8:30 AM
 * @version: 1.0
 */
public class Person {

    private Integer id;
    private String name;
    private Integer age;
    private String sex;
    private IdCard card;

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public Integer getAge() {
        return age;
    }

    public void setAge(Integer age) {
        this.age = age;
    }

    public String getSex() {
        return sex;
    }

    public void setSex(String sex) {
        this.sex = sex;
    }

    public IdCard getCard() {
        return card;
    }

    public void setCard(IdCard card) {
        this.card = card;
    }

    @Override
    public String toString() {
        return "Person{" + "id=" + id + ", name='" + name + ",
        age=" + age + ", sex='" + sex + ", card=" + card + '}';
    }
}
```

3. *PersonMapper.xml : SQL映射文件*
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="pers.huangyuhui.mybatis.mapper.PersonMapper">
    <!-- 嵌套查询：通过执行另一个SQL映射语句来返回预期的特殊类型 -->
    <select id="findPersonById" parameterType="integer" resultMap="PersonInfo">
        SELECT * FROM tb_person WHERE id = #{id}
    </select>
    <resultMap id="PersonInfo" type="person">
        <id property="id" column="id"/>
        <result property="name" column="name"/>
        <result property="age" column="age"/>
        <result property="sex" column="sex"/>
        <!-- 一对一: association使用select属性引入另一条SQL语句 -->
        <association property="card"
                     column="card_id"
                     javaType="idCard"
                     select="pers.huangyuhui.mybatis.mapper.IdCardMapper.findCodeById"/>
    </resultMap>
    
    <!-- 嵌套结果查询: 使用嵌套结果映射来处理重复的联合结果的子集 -->
    <select id="findPersonById2" parameterType="integer" resultMap="PersonInfo2">
        SELECT p.*,c.code FROM tb_person p,tb_idcard c WHERE p.card_id=c.id AND p.id=#{id}
    </select>
    <resultMap id="PersonInfo2" type="person">
        <id property="id" column="id"/>
        <result property="name" column="name"/>
        <result property="age" column="age"/>
        <result property="sex" column="sex"/>
        <association property="card" javaType="idCard">
            <id property="id" column="id"/>
            <result property="code" column="code"/>
        </association>
    </resultMap>
</mapper>
```

4. *MyBatisTest.java : 测试类*
```java
/**
 * @project: mybatis_associated_mapping
 * @description: 测试
 * @author: HuangYuhui
 * @date: 6/1/2019-9:43 AM
 * @version: 1.0
 */
public class MyBatisTest {

    @Test
    // TODO: 6/8/2019 一对一: 嵌套查询方式 
    public void findPersonById() {
        SqlSession sqlSession = MyBatisUtils.getSession();
        Person info = sqlSession.selectOne("pers.huangyuhui.mybatis.mapper.PersonMapper.findPersonById2", 1);
        System.out.println(info);

        sqlSession.close();
    }
}
```



### 一对多
*以一个用户对应多个订单为例,示例程序如下 :*

1. *SQL 数据表文件*
```sql
-- auto-generated definition by Intellij IDEA
create table user
(
    id      int auto_increment primary key,
    name    varchar(15) null,
    address varchar(50) null
);

create table tb_order
(
    id      int auto_increment primary key,
    number  varchar(20) not null,
    user_id int(2)      not null,
    constraint orders_user_id_fk foreign key (user_id) references user (id)
);
```

2. *Java Bean : User.java and Order.java*
```java
package pers.huangyuhui.mybatis.bean;

import java.util.List;

/**
 * @project: mybatis_associated_mapping
 * @description: 用户信息
 * @author: HuangYuhui
 * @date: 6/1/2019-11:04 AM
 * @version: 1.0
 */
public class User {

    private Integer id;
    private String name;
    private String address;
    private List<Order> orders;

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getAddress() {
        return address;
    }

    public void setAddress(String address) {
        this.address = address;
    }

    public List<Order> getOrders() {
        return orders;
    }

    public void setOrders(List<Order> orders) {
        this.orders = orders;
    }

    @Override
    public String toString() {
        return "User{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", address='" + address + '\'' +
                ", orders=" + orders +
                '}';
    }
}



package pers.huangyuhui.mybatis.bean;

import java.util.List;

/**
 * @project: mybatis_associated_mapping
 * @description: 订单信息
 * @author: HuangYuhui
 * @date: 6/1/2019-11:02 AM
 * @version: 1.0
 */
public class Order {
    private Integer id;
    private String number;
    private List<Product> products;

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getNumber() {
        return number;
    }

    public void setNumber(String number) {
        this.number = number;
    }

    public List<Product> getProducts() {
        return products;
    }

    public void setProducts(List<Product> products) {
        this.products = products;
    }

    @Override
    public String toString() {
        return "Order{" +
                "id=" + id +
                ", number='" + number + '\'' +
                ", products=" + products +
                '}';
    }
}
```

3. *UserMapper.xml : SQL映射文件*
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="pers.huangyuhui.mybatis.mapper.UserMapper">
    <!-- 一对多:查看某一用户及其关联的订单信息 注意:当关联查询出的列名相同时,需要使用别名区分 -->
    <select id="findUserWithOrders" parameterType="integer" resultMap="UserWithOrdersResult">
        SELECT u.*, o.id as order_id, o.number
        FROM user u,tb_order o
        WHERE u.id = o.user_id AND u.id = #{id}
    </select>

    <resultMap id="UserWithOrdersResult" type="user">
        <id property="id" column="id"/>
        <result property="name" column="name"/>
        <result property="address" column="address"/>
        <!-- ofType:属性集合中的元素类型 -->
        <collection property="orders" ofType="order">
            <!-- 'order_id':tb_order.id的字段别名 -->
            <id property="id" column="order_id"/>
            <result property="number" column="number"/>
        </collection>
    </resultMap>

</mapper>
```

4. *MyBatisTest.java : 测试类*
```java
/**
 * @project: mybatis_associated_mapping
 * @description: 测试
 * @author: HuangYuhui
 * @date: 6/1/2019-9:43 AM
 * @version: 1.0
 */
public class MyBatisTest {

    @Test
    // TODO: 6/8/2019 一对多:测试查询客户及其订单信息操作 
    public void findUserWithOrders() {
        SqlSession sqlSession = MyBatisUtils.getSession();
        User info = sqlSession.selectOne("pers.huangyuhui.mybatis.mapper.UserMapper.findUserWithOrders", 1);
        System.out.println(info);

        sqlSession.close();
    }
}
```



### 多对多
*以订单与商品的关系为例(一个订单可以包含多个商品),示例程序如下 :*

1. *SQL 数据表文件*
```sql
-- auto-generated definition by Intellij IDEA
-- 商品表
create table tb_product
(
    id    int auto_increment primary key,
    name  varchar(25) null,
    price double      null
);

-- 订单表
create table tb_order
(
    id      int auto_increment primary key,
    number  varchar(20) not null,
    user_id int(2)      not null,
    constraint orders_user_id_fk foreign key (user_id) references user (id)
);

-- 商品与订单表的中间表
create table tb_order_product
(
    id         int auto_increment primary key,
    order_id   int null,
    product_id int null,
    constraint tb_order_product_tb_order_id_fk foreign key (order_id) references tb_order (id),
    constraint tb_order_product_tb_product_id_fk foreign key (product_id) references tb_product (id)
);
```

2. *ProductMapper.xml : SQL映射文件*
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="pers.huangyuhui.mybatis.mapper.ProductMapper">
    <!-- 根据商品id查询指定商品及其对应的订单信息,注意:当关联查询出的列名相同时,需要使用别名区分 -->
    <select id="findProductWithOrder" resultMap="ProductWithOrder">
        SELECT p.*, o.id as oid, o.number
        FROM tb_product p,tb_order o,tb_order_product op
        WHERE o.id = op.order_id AND p.id = op.product_id AND p.id = #{id}
    </select>
    <!-- 自定义手动映射类型 -->
    <resultMap id="ProductWithOrder" type="product">
        <id property="id" column="id"/>
        <result property="name" column="name"/>
        <result property="price" column="price"/>
        <!-- 多对多关联映射 -->
        <collection property="orders" ofType="order">
            <!-- 'oid':tb_order.id的别名 -->
            <id property="id" column="oid"/>
            <result property="id" column="id"/>
            <result property="number" column="number"/>
        </collection>
    </resultMap>
</mapper>
```

3. *MyBatisTest.java : 测试类*
```java
package pers.huangyuhui.mybatis.test;

import org.apache.ibatis.session.SqlSession;
import org.junit.Ignore;
import org.junit.Test;
import pers.huangyuhui.mybatis.bean.Order;
import pers.huangyuhui.mybatis.bean.Person;
import pers.huangyuhui.mybatis.bean.Product;
import pers.huangyuhui.mybatis.bean.User;
import pers.huangyuhui.mybatis.util.MyBatisUtils;

/**
 * @project: mybatis_associated_mapping
 * @description: 测试
 * @author: HuangYuhui
 * @date: 6/1/2019-9:43 AM
 * @version: 1.0
 */
public class MyBatisTest {

    @Test
    // TODO: 6/8/2019 多对多:根据商品id查询指定商品及其对应的订单信息
    public void findProductWithOrder() {
        SqlSession sqlSession = MyBatisUtils.getSession();
        Product info = sqlSession.selectOne("pers.huangyuhui.mybatis.mapper.ProductMapper.findProductWithOrder", 3);
        System.out.println(info);
    }
}
```

4. *程序运行结果*
```
==>  Preparing: SELECT p.*, o.id as oid, o.number FROM tb_product p,tb_order o,tb_order_product op 
WHERE o.id = op.order_id AND p.id = op.product_id AND p.id = ? 
==> Parameters: 3(Integer)
<== Total: 3

Product{id=3, name='<book:Spring MVC>', price=33.3, orders=[Order{id=3, number='10000001', products=null}, Order{id=3, number='10000003', products=null}, Order{id=3, number='10000002', products=null}]}
```
---
title: DBUtils
date: 2019-05-30 17:28:41
tags: [Java,JDBC]
---

## 学习笔记 : DBUtils工具
*简介 : 为了更加简单地使用`JDBC`,Apache组织提供了一个工具类库`commons-dbutils`,它是操作数据库的一个组件,实现了对JDBC的简单封装,可以在不影响性能的情况下极大地简化JDBC的编码工作量.*

### API 介绍
*简介 : `commons-dbutils`的核心是两个类为`DbUtils`,`QueryRunner`,和一个接口`ResultSetHandler`*.
1. *`org.apache.commons.dbutils.DbUtils` : 该类主要为如何关闭数据库连接,装载JDBC驱动程序之类的常规工作提供静态方法.*
2. *`org.apache.commons.dbutils.QueryRunner` : 该类简化了执行SQL语句的代码,它与`ResultSetHandler`组合在一起就能完成大部分的数据库操作,大大减少编码量.*
3. *`org.apache.commons.dbutils.ResultSetHandler` : 该接口用于处理ResultSet结果集,它可以将结果集中的数据转换为不同的形式.*

### CURE 案例
1. *数据表信息*
```sql
-- auto-generated definition by Intellij IDEA
create table user
(
    id       int auto_increment primary key,
    name     varchar(15) null,
    password varchar(20) null
)
```

2. *C3P0配置文件*
```xml
<?xml version="1.0" encoding="UTF-8"?>
<c3p0-config>
    <!-- 默认配置: 在没有指定配置时默认使用该配置创建c3p0数据源对象 -->
    <default-config>
        <property name="user">xxxxxx</property>
        <property name="password">xxxxxx</property>
        <property name="driverClass">com.mysql.cj.jdbc.Driver</property>
        <property name="jdbcUrl">
            jdbc:mysql://LOCALHOST/test?useSSL=false&amp;serverTimezone=UTC&amp;allowPublicKeyRetrieval=true
        </property>
        <property name="checkoutTimeout">30000</property>
        <property name="initialPoolSize">5</property>
        <property name="maxIdleTime">600</property>
        <property name="maxPoolSize">10</property>
        <property name="minPoolSize">1</property>
        <property name="maxStatements">200</property>
    </default-config>

    <!-- 自定义配置 -->
    <named-config name="yu">
        <property name="user">xxxxxx</property>
        <property name="password">xxxxxx</property>
        <property name="driverClass">com.mysql.cj.jdbc.Driver</property>
        <property name="jdbcUrl">
            jdbc:mysql://LOCALHOST/test?useSSL=false&amp;serverTimezone=UTC&amp;allowPublicKeyRetrieval=true
        </property>
        <property name="initiaPoolSize">5</property>
        <property name="maxPoolSize">15</property>
    </named-config>
</c3p0-config>
```

3. *数据源工具类*
```java
package pers.huangyuhui.dbutils.utils;

import com.mchange.v2.c3p0.ComboPooledDataSource;

import javax.sql.DataSource;

/**
 * @project: dbutils
 * @package: pers.huangyuhui.dbutils.utils
 * @description: 创建数据源
 * @author: HuangYuhui
 * @date: 5/31/2019-7:00 PM
 * @version: 1.0
 */
public class C3P0Utils {

    private static DataSource dataSource;

    //创建数据源
    static {
        dataSource = new ComboPooledDataSource("yu");
    }

    //获取数据源
    public static DataSource getDataSource() {
        return dataSource;
    }

}
```

4. *Java Bean : 存储用户信息*
```java
package pers.huangyuhui.dbutils.bean;

/**
 * @project: dbutils
 * @package: pers.huangyuhui.dbutils.bean
 * @description: 用户信息
 * @author: HuangYuhui
 * @date: 5/31/2019-7:13 PM
 * @version: 1.0
 */
public class User {

    private Integer id;
    private String name;
    private String password;

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

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }

    @Override
    public String toString() {
        return "User{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", password='" + password + '\'' +
                '}';
    }
}
```

5. *DAO层 : 封装数据库的基本操作*
```java
package pers.huangyuhui.dbutils.dao;

import org.apache.commons.dbutils.QueryRunner;
import org.apache.commons.dbutils.handlers.BeanHandler;
import org.apache.commons.dbutils.handlers.BeanListHandler;
import pers.huangyuhui.dbutils.bean.User;
import pers.huangyuhui.dbutils.utils.C3P0Utils;

import javax.sql.DataSource;
import java.sql.SQLException;
import java.util.List;

/**
 * @project: dbutils
 * @package: pers.huangyuhui.dbutils
 * @description: 数据库的基本操作
 * @author: HuangYuhui
 * @date: 5/31/2019-7:07 PM
 * @version: 1.0
 */
public class DBUtilsDao {

    //获取数据源
    private static DataSource dataSource = C3P0Utils.getDataSource();
    //创建queryRunner对象
    private static QueryRunner queryRunner = new QueryRunner(dataSource);
    //sql
    private String SQL_SELECT = "select id,name,password from user where id = ?";
    private String SQL_UPDATE = "update user set name=?,password=? where id=?";
    private String SQL_INSERT = "insert into user(name,password) values(?,?)";
    private String SQL_SELECTALL = "select id,name,password from user";
    private String SQL_DELETE = "delete from user where id=?";

    // TODO: 6/8/2019 查询所有用户信息
    public List selectAll() throws SQLException {
        //获取用户列表
        List<User> list = queryRunner.query(SQL_SELECTALL, new BeanListHandler<User>(User.class));
        return list;
    }

    // TODO: 6/8/2019 查询单个用户信息
    public User select(int id) throws SQLException {
        //获取用户信息
        User user = queryRunner.query(SQL_SELECT, new BeanHandler<>(User.class), new Object[]{id});
        return user;
    }

    // TODO: 6/8/2019 添加用户信息
    public boolean insert(User user) throws SQLException {
        //判断是否添加成功
        int num = queryRunner.update(SQL_INSERT, new Object[]{user.getName(), user.getPassword()});
        if (num > 0) {
            return true;
        }
        return false;
    }

    // TODO: 6/8/2019 修改用户信息
    public boolean update(User user) throws SQLException {
        int num = queryRunner.update(SQL_UPDATE, new Object[]{user.getName(), user.getPassword(), user.getId()});
        if (num > 0) {
            return true;
        }
        return false;
    }

    // TODO: 6/8/2019 删除用户信息
    public boolean delete(int id) throws SQLException {
        int num = queryRunner.update(SQL_DELETE, id);
        if (num > 0) {
            return true;
        } else {
            return false;
        }
    }
}
```

6. *测试类*
```java
package pers.huangyuhui.dbutils.test;

import org.junit.BeforeClass;
import org.junit.Ignore;
import org.junit.Test;
import pers.huangyuhui.dbutils.bean.User;
import pers.huangyuhui.dbutils.dao.DBUtilsDao;

import java.sql.SQLException;
import java.util.List;

/**
 * @project: dbutils
 * @description: 测试数据库的基本操作
 * @author: HuangYuhui
 * @date: 5/31/2019-7:45 PM
 * @version: 1.0
 */
public class DBUtilsDaoTest {

    private static User user;
    private static DBUtilsDao dbUtilsDao;

    @BeforeClass
    public static void init() {
        user = new User();
        dbUtilsDao = new DBUtilsDao();
    }

    @Ignore
    @Test
    // TODO: 6/8/2019 测试添加用户操作
    public void insertTest() throws SQLException {
        user.setName("YUbuntu0109");
        user.setPassword("MyPassword");
        if (dbUtilsDao.insert(user)) {
            System.out.println("success to insert a user information !");
        } else {
            System.out.println("fail to insert a new user information !");
        }
    }


    @Ignore
    @Test
    // TODO: 6/8/2019 测试修改用户信息操作 
    public void updateTest() throws SQLException {
        user.setId(1);
        user.setName("YUbuntu0109-1");
        user.setPassword("MyPassword-1");
        if (dbUtilsDao.update(user)) {
            System.out.println("success to update the user information !");
        } else {
            System.out.println("fail to update the user information !");
        }
    }

    @Ignore
    @Test
    // TODO: 6/8/2019 测试删除用户操作
    public void deleteTest() throws SQLException {
        if (dbUtilsDao.delete(1)) {
            System.out.println("success to delete the user information !");
        } else {
            System.out.println("fail to delete the user information !");
        }
    }

    @Ignore
    @Test
    // TODO: 6/8/2019 测试查询单个用户信息操作
    public void selectTest() throws SQLException {
        System.out.println(dbUtilsDao.select(1));
    }

    @Ignore
    @Test
    // TODO: 6/8/2019 测试查询所有用户信息操作
    public void selectAllTest() throws SQLException {
        List users = dbUtilsDao.selectAll();
        System.out.println(users);
    }
}
```
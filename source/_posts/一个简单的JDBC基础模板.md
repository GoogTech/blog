---
title: 一个简单的JDBC基础模板
date: 2019-09-04 20:55:02
tags: [Java,MySQL,JDBC]
---

## 学习笔记 : 一个简单的JDBC基础模板
*最近在 GitHub 上又结识了一个小伙伴,看他最近在学习 JDBC 方面的知识,看到他所写的代码让我顿时回忆起自己初学编程的自己,所以想帮帮他,继而把前些日子写的 JDBC 基础模板整理了一下并给他提了一个 PR,详情请参考 : https://github.com/qinjiajuny/JDBCLearning/pull/4*

1. *tb_user : 用户信息数据表*
```sql
-- auto-generated definition
create table tb_user
(
    id        int auto_increment primary key,
    name      varchar(10) null,
    age       int         null,
    gender    char(2)     null,
    password  varchar(15) null,
    telephone varchar(11) null,
    birthday  date        null
);


```

2. *db.properties : 数据库连接配置文件*
```sh
# Database configuration information (Server version: 8.0.11 MySQL Community Server - GPL)
Url=jdbc:mysql://localhost/database-name?useSSL=false&serverTimezone=UTC&allowPublicKeyRetrieval=true
UserName=
UserPassword=
DriverName=com.mysql.cj.jdbc.Driver
```

3. *ConfigUtil.java : 读取数据库连接配置文件的工具类*
```java
package pers.huangyuhui.curd.utils;

import java.io.FileNotFoundException;
import java.io.IOException;
import java.io.InputStream;
import java.util.Properties;

/**
 * @project: curd-demo
 * @description: 读取数据库配置文件的工具类
 * @author: 黄宇辉
 * @date: 8/22/2019-3:49 PM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
class ConfigUtil {

    private ConfigUtil() {
    }

    private static Properties properties;
    // 读取数据库配置文件
    private static InputStream inputStream = ConfigUtil.class.getResourceAsStream("/db.properties");

    static {
        try {
            properties = new Properties();
            properties.load(inputStream);
            properties.getProperty("Url");
            properties.getProperty("UserName");
            properties.getProperty("UserPassword");
            properties.getProperty("DriverName");
        } catch (FileNotFoundException e) {
            System.err.println("error: not found db.properties file");
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }

    }

    //获取数据库配置
    static Properties getProperties() {
        return properties;
    }
}
```

4. *DBUtil : 连接数据库的工具类*
```java
package pers.huangyuhui.curd.utils;

import java.sql.Connection;
import java.sql.DriverManager;

/**
 * @project: curd-demo
 * @description: 链接数据库的工具类
 * @author: 黄宇辉
 * @date: 8/22/2019-3:30 PM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public class DBUitl {

    private static Connection connection;

    //获取数据库配置信息
    private static String URL = ConfigUtil.getProperties().getProperty("Url");
    private static String USER = ConfigUtil.getProperties().getProperty("UserName");
    private static String PASSWORD = ConfigUtil.getProperties().getProperty("UserPassword");
    private static String DRIVER_NAME = ConfigUtil.getProperties().getProperty("DriverName");

    //加载数据库驱动
    static {
        try {
            Class.forName(DRIVER_NAME);
        } catch (Exception e) {
            System.err.println("error: fail to init the driver of database");
            throw new ExceptionInInitializerError(e);
        }
    }

    //获取数据库连接
    public static Connection getConnection() {
        if (connection == null) {
            try {
                connection = DriverManager.getConnection(URL, USER, PASSWORD);
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
        return connection;
    }
}
```

5. *User.java : 用户信息类*
```java
package pers.huangyuhui.curd.bean;

/**
 * @project: curd-demo
 * @description: 用户基础信息
 * @author: 黄宇辉
 * @date: 8/22/2019-3:22 PM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public class User {
    private Integer id;
    private String name;
    private Integer age;
    private String gender;
    private String password;
    private String telephone;
    private String birthday;

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

    public String getGender() {
        return gender;
    }

    public void setGender(String gender) {
        this.gender = gender;
    }

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }

    public String getTelephone() {
        return telephone;
    }

    public void setTelephone(String telephone) {
        this.telephone = telephone;
    }

    public String getBirthday() {
        return birthday;
    }

    public void setBirthday(String birthday) {
        this.birthday = birthday;
    }

    @Override
    public String toString() {
        return "User{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", age=" + age +
                ", gender='" + gender + '\'' +
                ", password='" + password + '\'' +
                ", telephone='" + telephone + '\'' +
                ", birthday='" + birthday + '\'' +
                '}';
    }
}
```

6. *UserDao.java : 数据访问层*
```java
package pers.huangyuhui.curd.dao;

import pers.huangyuhui.curd.bean.User;
import pers.huangyuhui.curd.utils.DBUitl;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.util.ArrayList;
import java.util.List;

/**
 * @project: curd-demo
 * @description: 数据访问层(DAO):可利用JDK8新特性Lambda来优化此程序
 * @author: 黄宇辉
 * @date: 8/22/2019-3:28 PM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public class UserDao {

    //获取数据库连接
    private static Connection connection = DBUitl.getConnection();

    //添加用户信息
    public boolean insertNewUser(User user) {
        String sql = "insert into tb_user(name, age, gender, password, telephone, birthday) values (?,?,?,?,?,?)";
        try (PreparedStatement preparedStatement = connection.prepareStatement(sql)) {
            preparedStatement.setString(1, user.getName());
            preparedStatement.setInt(2, user.getAge());
            preparedStatement.setString(3, user.getGender());
            preparedStatement.setString(4, user.getPassword());
            preparedStatement.setString(5, user.getBirthday());
            preparedStatement.setString(6, user.getTelephone());
            return preparedStatement.executeUpdate() > 0;
        } catch (SQLException e) {
            e.printStackTrace();
        }
        return false;
    }

    //查询所有用户信息
    public List<User> selectAllUser() {
        String sql = "select id, name, age, gender, password, telephone, birthday from tb_user";
        List<User> list = new ArrayList<>();
        try (PreparedStatement preparedStatement = connection.prepareStatement(sql);
             ResultSet resultSet = preparedStatement.executeQuery()) {
            while (resultSet.next()) {
                User user = new User();
                user.setId(resultSet.getInt("id"));
                user.setName(resultSet.getString("name"));
                user.setAge(resultSet.getInt("age"));
                user.setGender(resultSet.getString("gender"));
                user.setPassword(resultSet.getString("password"));
                user.setTelephone(resultSet.getString("telephone"));
                user.setBirthday(resultSet.getString("birthday"));
                list.add(user);
            }
            return list;
        } catch (SQLException e) {
            e.printStackTrace();
        }
        return null;
    }

    //更新用户信息
    public boolean updateUserById(User user) {
        String sql = "UPDATE tb_user SET name = ? , gender = ? , age = ? , password = ?, birthday = ?, telephone = ? where id = ?";
        try {
            PreparedStatement preparedStatement = connection.prepareStatement(sql);
            preparedStatement.setString(1, user.getName());
            preparedStatement.setString(2, user.getGender());
            preparedStatement.setInt(3, user.getAge());
            preparedStatement.setString(4, user.getPassword());
            preparedStatement.setString(5, user.getBirthday());
            preparedStatement.setString(6, user.getTelephone());
            preparedStatement.setInt(7, user.getId());
            return preparedStatement.executeUpdate() > 0;
        } catch (SQLException e) {
            e.printStackTrace();
        }
        return false;
    }

    //删除用户信息
    public boolean deleteUserById(Integer id) {
        String sql = "delete from tb_user where id = ?";
        try {
            PreparedStatement preparedStatement = connection.prepareStatement(sql);
            preparedStatement.setInt(1, id);
            return preparedStatement.executeUpdate() > 0;
        } catch (SQLException e) {
            e.printStackTrace();
        }
        return false;
    }

}
```

7. *UserService.java : 业务逻辑层*
```java
package pers.huangyuhui.curd.service;

import pers.huangyuhui.curd.bean.User;

import java.util.List;

/**
 * @project: curd-demo
 * @description: 业务逻辑层(Service)
 * @author: 黄宇辉
 * @date: 8/22/2019-3:29 PM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public interface UserService {

    List<User> getAllUser();

    boolean addUser(User user);

    boolean updateUser(User user);

    boolean deleteUser(Integer id);
}
```

8. *UserServiceImpl.java : 业务逻辑层的实现类*
```java
package pers.huangyuhui.curd.service.impl;

import pers.huangyuhui.curd.bean.User;
import pers.huangyuhui.curd.dao.UserDao;
import pers.huangyuhui.curd.service.UserService;

import java.util.List;

/**
 * @project: curd-demo
 * @description: 业务逻辑层的实现类
 * @author: 黄宇辉
 * @date: 8/22/2019-3:29 PM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public class UserServiceImpl implements UserService {

    private static UserDao userDao = new UserDao();

    @Override
    public List<User> getAllUser() {
        return userDao.selectAllUser();
    }

    @Override
    public boolean addUser(User user) {
        return userDao.insertNewUser(user);
    }

    @Override
    public boolean updateUser(User user) {
        return userDao.updateUserById(user);
    }

    @Override
    public boolean deleteUser(Integer id) {
        return userDao.deleteUserById(id);
    }
}
```

9. *AppTest.java : 程序测试类*
```java
package pers.huangyuhui.curd;

import org.junit.BeforeClass;
import org.junit.Test;
import pers.huangyuhui.curd.bean.User;
import pers.huangyuhui.curd.service.UserService;
import pers.huangyuhui.curd.service.impl.UserServiceImpl;

import java.util.List;

/**
 * Unit test for simple App.
 */
public class AppTest {
    private static UserService userService;

    @BeforeClass
    public static void init() {
        userService = new UserServiceImpl();
    }

    //test getAllUser() method
    @Test
    public void printAllUser() {
        List<User> allUsers = userService.getAllUser();
        System.out.println("获取所有的用户信息 : " + allUsers);
    }

    //test addUser method
    @Test
    public void addUser() {
        User user = new User();
        user.setName("addUser");
        user.setGender("男");
        user.setAge(0);
        user.setPassword("pwd");
        user.setTelephone("000000");
        user.setBirthday("0000-00-00");
        System.out.println("是否成功添加用户信息 : " + userService.addUser(user));
    }


    //test updateUser method
    @Test
    public void updateUser() {
        User user = new User();
        user.setId(1);
        user.setName("updateUser");
        user.setGender("女");
        user.setAge(0);
        user.setPassword("pwd");
        user.setTelephone("111111");
        user.setBirthday("1111-11-11");
        System.out.println("是否成功更新用户信息 : " + userService.updateUser(user));
    }

    //test deleteUser method
    @Test
    public void deleteUser() {
        System.out.println("是否成功删除用户信息 : " + userService.deleteUser(1));
    }
}
```
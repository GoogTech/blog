---
title: Python操作MySQL数据库之基本的CURD
date: 2019-11-15 20:56:04
tags: [Python,MySQL]
---

## 学习笔记 : Python 操作 MySQL 数据库之基本的 CURD

*🐍 学习完 Python 的基础知识后，当然需要进阶地学习关于 Python 的数据库编程知识啦~ 下面是我写的一个操作 MySQL 数据库的基本增删改查，其对于和我一样初学 Python 的同学来说，非常具有参考与学习价值哟~*


1. *tb_user : 数据库表结构*
```sql
CREATE TABLE `tb_user` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `name` varchar(45) COLLATE utf8mb4_unicode_ci DEFAULT NULL,
  `sex` char(1) COLLATE utf8mb4_unicode_ci DEFAULT NULL,
  `phone` varchar(45) COLLATE utf8mb4_unicode_ci DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=9 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci
```


2. *dbconfig.ini : 存储数据库的配置信息*
```ini
[DATABASE_CONFIGURATION_INFOS]
host = localhost
user = root
password = 
database = 
charset = utf8
```


3. *Utils.py : 连接数据库的工具类*
```python
"""
Project name : python-learning
Description : A SIMPLE TOOL FOR OPERATING DATABASE
Author : 黄宇辉
Version : 1.0
Website : https://yubuntu0109.github.io/
Created on 11/12/2019-9:30 PM
"""

import configparser
import pymysql


class DBUtils:

    """
    get the databse configuration infos from the specified file
    """
    @staticmethod
    def getConnConfig():
        try:
            configpar = configparser.ConfigParser()
            configpar.read("dbconfig.ini", encoding='utf-8')
            connConfig_items = dict(configpar.items('DATABASE_CONFIGURATION_INFOS'))
            return connConfig_items

        except FileNotFoundError as e:
            print("CATCH THE EXCEPTION INFO : %s" % e)

    """
    get the database connection object
    """
    @staticmethod
    def getConnection():
        try:
            global connection  # defined a global connection object
            connConfig = DBUtils.getConnConfig()  # get the database configuration infos
            connection = pymysql.connect(host=connConfig.get('host'),
                                         user=connConfig.get('user'),
                                         password=connConfig.get('password'),
                                         database=connConfig.get('database'),
                                         charset=connConfig.get('charset'))
            return connection
        except Exception as e:
            print("FAIL TO CONNECT TO THE DATABASE : %s" % e)
```


4. *CURD.py : 操作数据库的测试类*
```python
"""
Project name : python-learning
Description : BASIC CURD ON MYSQL DATABASE
Author : 黄宇辉
Version : 1.0
Website : https://yubuntu0109.github.io/
Created on 11/11/2019-10:08 PM
"""

import pymysql
from Utils import DBUtils  # format : from filename import classname


class CURDTest:

    def __init__(self):
        global connection  # defined a global variable
        connection = DBUtils.getConnection()  # get the database connection

    """
    select the specified user info by id
    """
    @classmethod
    def selectById(cls, userid):
        try:
            # create the cursor object
            with connection.cursor() as cursor:
                # defined the sql statement
                sql = 'SELECT id,name,sex,phone FROM tb_user WHERE id = %s'
                # execute the specified sql statement
                cursor.execute(sql, [userid])
                # get the result
                row = cursor.fetchone()
                # ouput the result
                if row is not None:
                    print('ID:{0} - NAME:{1} - SEX:{2} - PHONE:{3}'.format(row[0], row[1], row[2], row[3]))
                else:
                    print("NOT FOUND THE SPECIFIED USER INFO BY ID : %s" % userid)

        # catch the exception about database
        except pymysql.DatabaseError as e:
            print(e)
        # release the connection resource
        finally:
            connection.close()

    """
    select all of user infos
    """
    @classmethod
    def selectAll(cls):
        try:
            with connection.cursor() as cursor:
                sql = 'SELECT id,name,sex,phone FROM tb_user'
                cursor.execute(sql)
                result_set = cursor.fetchall()
                for row in result_set:
                    print('ID:{0} - NAME:{1} - SEX:{2} - PHONE:{3}'.format(row[0], row[1], row[2], row[3]))
        except pymysql.DatabaseError as e:
            print(e)
        finally:
            connection.close()

    """
    insert the user info
    """
    @classmethod
    def addUser(cls, username, sex, telephone):
        try:
            with connection.cursor() as cursor:
                sql = 'INSERT INTO tb_user(name,sex,phone) VALUE (%s,%s,%s)'
                affectedcount = cursor.execute(sql, (username, sex, telephone))
                print('inserted successfully and the affected count : %s' % affectedcount)
                connection.commit()
        except pymysql.DatabaseError as e:
            print(e)
            connection.rollback()
        finally:
            connection.close()

    """
    update the user info
    """
    @classmethod
    def updateUser(cls, username, sex, telephone, userid):
        try:
            with connection.cursor() as cursor:
                sql = 'UPDATE tb_user SET name = %s, sex = %s, phone = %s WHERE id = %s'
                affectedcount = cursor.execute(sql, (username, sex, telephone, userid))
                print('updated successfully and the affected count : %s' % affectedcount)
                connection.commit()
        except pymysql.DatabaseError as e:
            print(e)
            connection.rollback()
        finally:
            connection.close()

    """
    delete the user info
    """
    @classmethod
    def delUser(cls, userid):
        try:
            with connection.cursor() as cursor:
                sql = 'DELETE FROM tb_user WHERE id = %s'
                affectedcount = cursor.execute(sql, userid)
                print("deleted successfully and the affected count : %s" % affectedcount)
                connection.commit()
        except pymysql.DatabaseError as e:
            print(e)
            connection.rollback()
        finally:
            connection.close()


"""
test
"""
curd = CURDTest()
curd.selectAll()
# curd.selectById(userid=1)
# curd.addUser(username='addUser', sex='m', telephone='00000000000')
# curd.updateUser(username='updateUser', sex='f', telephone='11111111111', userid=1)
# curd.delUser(userid=1)
```


5. *程序运行结果如下所示( 仅供参考 ) :*
```
ID:1 - NAME:黄宇辉 - SEX:男 - PHONE:11111111111
ID:2 - NAME:燕子 - SEX:女 - PHONE:00000000000
```

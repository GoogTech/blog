---
title: hello Python
date: 2019-11-08 19:45:04
tags: [Python]
---

## 学习笔记 : hello Python

### Python 简介
*Python ( 英国发音：`/ˈpaɪθən/`，美国发音：`/ˈpaɪθɑːn/` )，是一种面向对象的解释型计算机程序设计语言，由荷兰人 GuidovanRossum 于 1989 年发明，第一个公开发行版发行于 1991 年*

*Python 是纯粹的自由软件，源代码和解释器 CPython 遵循 GPL( GNUGeneralPublicLicense )协议. Python语法简洁清晰，特色之一是强制用空白符( whitespace )作为语句缩进*


### Python 的应用
* 系统编程
* 图形处理
* 数学处理
* 文本处理
* 数据库编程
* 网络编程
* Web编程
* 多媒体应用
* pymo引擎
* 黑客编程


### Python 的应用领域
* Web 应用开发
* 操作系统管理，服务器运维的自动化脚本
* 科学计算
* 桌面软件
* 服务器软件( 网络软件 )
* 游戏
* 构思实现，产品早期原型迭代


### Python 的应用方向
* 常规软件开发
* 科学计算
* 自动化运维
* 云计算
* WEB开发
* 网络爬虫
* 数据分析
* 人工智能


### Python 程序的执行原理
*Python 不同于类似 C/C++ 之类的编译性语言，其不需要编译成二进制代码，它可以直接从源代码运行程序. 既 Python 解释器先会将源代码转换为字节码( 并非二进制机器码，其需要进一步编译才能够被机器识别. 如果 Python 进程在机器上拥有写入权限，那么它将把程序的字节码保存为一个以`.pyc`为扩展名的文件 )，然后再把编译好的字节码转发到 Python 虚拟机( PVM )中进行执行.*


### 实现一个简单的学生管理系统
*早上在图书馆花了`2`个小时左右时间看完了 `《Python 快速编程入门》` 书籍的前六章，毕竟我是有编程基础的嘿嘿，况且编程语言之间是相通的. 为了熟悉 Python 函数的基本使用，晚上回到寝室写了一个简单的学生管理系统，相比 `hello world implemented by Python` 程序是不是有点急啦哈哈哈~ 代码如下所示 :*

```python
"""
Project name : python-learning
Description : A SIMPLE STUDENT MANAGEMENT SYSTEM
Author : 黄宇辉
Version : 1.0
Website : https://yubuntu0109.github.io/
Created on 11/8/2019-6:47 PM
"""

# CREATE A LIST WHICH BE USED TO STORE ALL OF STUDENT INFORMATIONS
student_infos = []


# OUTPUT THE MENU INFO INTO THE CONSOLE
def print_menu():
    print("=" * 50)
    print("### A SIMPLE STUDENT MANAGEMENT SYSTEM v1.0 ###")
    print("1:ADD THE STUDENT INFORMATION")
    print("2:DELETE THE STUDENT INFORMATION")
    print("3:MODIFY THE STUDENT INFORMATION")
    print("4:DISPLAY ALL OF STUDENT INFORMATIONS")
    print("5:SAVE ALL OF STUDENT INFORMATIONS")
    print("0:EXIT")
    print("=" * 50)


# ADD A NEW STUDENT INFORMATION
def add_info():
    new_name = input("PLEASE ENTER THE NEW STUDENT'S NAME: ")
    new_sex = input("PLEASE ENTER THE NEW STUDENT'S SEX: ")
    new_phone = input("PLEASE ENTER THE NEW STUDENT'S PHONE: ")
    new_info = {'name': new_name, 'sex': new_sex, 'phone': new_phone}
    student_infos.append(new_info)


# DELETE THE SPECIFIED STUDENT INFORMATION BY NO
def del_info():
    del_number = int(input("PLEASE THE SPECIFIED STUDENT' NO: ")) - 1  # index
    del student_infos[del_number]


# MODIFY THE SPECIFIED STUDENT INFORMATION BY NO
def modify_info():
    student_id = int(input("PLEASE THE STUDENT NO: "))
    new_student_name = input("PLEASE ENTER THE NEW NAME: ")
    new_student_sex = input("PLEASE ENTER THE NEW SEX: ")
    new_student_phone = input("PLEASE ENTER THE NEW PHONE: ")
    student_infos[student_id - 1]['name'] = new_student_name
    student_infos[student_id - 1]['sex'] = new_student_sex
    student_infos[student_id - 1]['phone'] = new_student_phone


# DISPLAY ALL OF STUDENT INFORMATIONS
def show_infos():
    print("=" * 50)
    print("ALL OF STUDENT INFORMATION BE SHOWED AS FOLLOW")
    print("=" * 50)
    print("NO       NAME        SEX     PHONE")
    i = 1
    for temp in student_infos:
        print("%d       %s      %s      %s" % (i, temp['name'], temp['sex'], temp['phone']))
        i += 1


# SAVE ALL OF STUDENT INFORMATIONS
def save_data():
    try:
        file = open("db.data", "w")
        file.write(str(student_infos))
        file.close()
    except FileNotFoundError:
        print("CATCH THE EXCEPTION: THE SPECIFIED FILE NOT EXIST!")
    else:  # so simple and so cool than Java
        print("SAVE ALL OF STUDENT INFORMATIONS SUCCESSFULLY")


# RECOVER ALL OF STUDENT INFORMATIONS
def recover_data():
    global student_infos
    try:
        file = open("db.data", "wb+")  # create new file if the specified file not exist
        content = file.read()
        print(content)  # throw the SyntaxError possibly: unexpected EOF while parsing
        student_infos = eval(content)
        file.close()
    except (SyntaxError, FileNotFoundError) as exception_info:
        print("CATCH THE EXCEPTION: %s" % exception_info)


# MAIN FUNCTION
def main():
    recover_data()  # the specified file must store some datas before reading
    while True:
        print_menu()
        key = input("PLEASE ENTER THE SPECIFIED NO OF MENU: ")
        if key == '1':
            add_info()
        elif key == '2':
            del_info()
        elif key == '3':
            modify_info()
        elif key == '4':
            show_infos()
        elif key == '5':
            save_data()
        elif key == '0':
            quit_confirm = input("ARE YOU SURE TO QUIT ?( Yes or No ): ")
            if quit_confirm == "Yes":
                break
            else:
                print("ERROR : PLEASE ENTER THE RIGHT COMMAND !")


# RUN
main()
```

*程序运行结果如下所示 :*
```
CATCH THE EXCEPTION: unexpected EOF while parsing (<string>, line 0)

==================================================
### A SIMPLE STUDENT MANAGEMENT SYSTEM v1.0 ###
1:ADD THE STUDENT INFORMATION
2:DELETE THE STUDENT INFORMATION
3:MODIFY THE STUDENT INFORMATION
4:DISPLAY ALL OF STUDENT INFORMATIONS
5:SAVE ALL OF STUDENT INFORMATIONS
0:EXIT
==================================================
PLEASE ENTER THE SPECIFIED NO OF MENU: 1
PLEASE ENTER THE NEW STUDENT'S NAME: 黄宇辉
PLEASE ENTER THE NEW STUDENT'S SEX: 男
PLEASE ENTER THE NEW STUDENT'S PHONE: 00000000000


==================================================
### A SIMPLE STUDENT MANAGEMENT SYSTEM v1.0 ###
1:ADD THE STUDENT INFORMATION
2:DELETE THE STUDENT INFORMATION
3:MODIFY THE STUDENT INFORMATION
4:DISPLAY ALL OF STUDENT INFORMATIONS
5:SAVE ALL OF STUDENT INFORMATIONS
0:EXIT
==================================================
PLEASE ENTER THE SPECIFIED NO OF MENU: 4

==================================================
ALL OF STUDENT INFORMATION BE SHOWED AS FOLLOW
==================================================
NO       NAME        SEX     PHONE
1        黄宇辉       男      00000000000


==================================================
### A SIMPLE STUDENT MANAGEMENT SYSTEM v1.0 ###
1:ADD THE STUDENT INFORMATION
2:DELETE THE STUDENT INFORMATION
3:MODIFY THE STUDENT INFORMATION
4:DISPLAY ALL OF STUDENT INFORMATIONS
5:SAVE ALL OF STUDENT INFORMATIONS
0:EXIT
==================================================
PLEASE ENTER THE SPECIFIED NO OF MENU: 5
SAVE ALL OF STUDENT INFORMATIONS SUCCESSFULLY

. . . . . .
```

*使用 Python 写完这个简单程序后，让我指间感觉超轻松，这语法太简洁了吧! 符合我的风格嘿嘿~*



> 推荐给初学 Python 同学的编程书籍 : 《Python 快速编程入门》- 黑马程序员编著  ( 本书对于现在的我来说，一周就可以完成全部的学习咯~ )

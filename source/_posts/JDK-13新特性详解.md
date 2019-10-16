---
title: JDK 13新特性详解
date: 2019-10-14 11:12:07
tags: [JDK]
---

### 学习笔记 : 详解JDK 13中的新特性

### 简介
*JDK 13 is the open-source reference implementation of version 13 of the Java SE Platform as specified by by `JSR 388` in the Java Community Process. JDK 13 reached General Availability on 17 September 2019. Production-ready binaries under the GPL are available from Oracle; binaries from other vendors will follow shortly. The features and schedule of this release were proposed and tracked via the JEP Process, as amended by the `JEP 2.0 proposal`. The release was produced using the `JDK Release Process (JEP 3)`.*


### 特性
* 350: 	Dynamic CDS Archives
* 351: 	ZGC: Uncommit Unused Memory
* 353: 	Reimplement the Legacy Socket API
* 354: 	Switch Expressions (Preview)
* 355: 	Text Blocks (Preview)


### 时间表
* 2019/06/13 		Rampdown Phase One (fork from main line)
* 2019/07/18 		Rampdown Phase Two
* 2019/08/08 		Initial Release Candidate
* 2019/08/22 		Final Release Candidate
* 2019/09/17 		General Availability


### Switch Expressions 特性演示
```java
 /*
JDK12之前版本的写法:许多break使它不必要地冗长,如果忘记写break,则会产生意料之外的结果或者异常,所以针对于此jdk12在这里进行了优化升级
*/
switch (score) {
    case 60:
        System.out.println("及格");
        break;
    case 80:
        System.out.println("良好");
        break;
    case 90:
        System.out.println("优秀");
        break;
    default:
        System.out.println("default");
        break;
}

/*
JDK12新特性写法:我们建议引入一种新形式的开关标签,写成"case L ->"表示如果标签匹配,则只执行标签右侧的代码
*/
switch (score) {
    case 60 -> System.out.println("及格");
    case 80 -> System.out.println("良好");
    case 90 -> System.out.println("优秀");
    default -> System.out.println("default");
}

/*
JDK13新特性写法:优化JDK12中的switch语法新特性,使其更加简洁~
*/
System.out.println(
        switch (score) {
            case 60 -> "及格";
            case 80 -> "良好";
            case 90 -> "优秀";
            default -> "default";
        }
);
```


### Text Blocks 特性演示
```java
//解释:左边的文本块相当于右边的字符串文字
/*
"""
line1
line2   ------>>>   "line 1\nline 2\nline 3\n"
line3
"""
*/


/*
HTML 示例程序
*/
//JDK13版本前的写法
String html = "<html>\n" +
        "    <body>\n" +
        "        <p>Hello, world</p>\n" +
        "    </body>\n" +
        "</html>\n";

//JDK13语法新特性的写法
String html2 =
        """
            <html>
                <body>
                    <p>Hello, world</p>
                </body>
            </html>
        """;


/*
SQL 示例程序
*/
 //JDK13版本前的写法
String query = "SELECT `EMP_ID`, `LAST_NAME` FROM `EMPLOYEE_TB`\n" +
        "WHERE `CITY` = 'INDIANAPOLIS'\n" +
        "ORDER BY `EMP_ID`, `LAST_NAME`;\n";

//JDK13新特性的写法
String query2 =
        """
        SELECT `EMP_ID`, `LAST_NAME` FROM `EMPLOYEE_TB`
        WHERE `CITY` = 'INDIANAPOLIS'
        ORDER BY `EMP_ID`, `LAST_NAME`;
        """;
```



> 👍For more information, please refer to : http://openjdk.java.net/projects/jdk/13/ and the document : https://docs.oracle.com/en/java/javase/13/

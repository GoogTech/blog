---
title: '小爬虫:JDBookCrawler-V3.0'
date: 2019-07-17 20:34:24
tags: [Java,WebMagic,MyBatis,MySQL,Crawler]
---

## 爬虫项目笔记 : JDBookCrawler-V3.0
*简介 : `JDBookCrawler 3.0`的爬虫功能基于`JDBookCrawler 2.0`,前端设计参考项目[`springboot-beginner`](https://github.com/YUbuntu0109/springboot-beginner),简单点说`v3.0`就等于`v2.0` + `springboot`,😅不尴尬嘿嘿嘿~*


### 开发环境
| 工具    | 版本或描述                |    
| ------- | ------------------------ |    
| `OS`    | Windows 10               | 
| `JDK`   |  1.8                     |    
| `IDE`   | IntelliJ IDEA 2019.1     |    
| `Maven` | 3.6.0                    |    
| `MySQL` | 8.0.11                   |

> 本项目的数据库版本为`8.0.11`，请广大版本为`5.0.0+`的同学注意咯：可通过逐个复制表结构来创建该数据库哟 ~


### 项目结构图
```
├─bookcrawler-v3.0
│  │  crawler.sql
│  │  pom.xml
│  │
│  │
│  └─src
│      └─main
│          ├─java
│          │  └─pers
│          │      └─huangyuhui
│          │          └─bookcrawler
│          │              │  BookcrawlerApplication.java
│          │              │
│          │              ├─controller
│          │              │      BookController.java
│          │              │
│          │              ├─crawler
│          │              │  │  BookCrawlerTest.java
│          │              │  │
│          │              │  ├─dao
│          │              │  │      BookDao.java
│          │              │  │
│          │              │  ├─mapper
│          │              │  │      BookMapper.xml
│          │              │  │
│          │              │  ├─pojo
│          │              │  │      Book.java
│          │              │  │
│          │              │  ├─resources
│          │              │  │      db.properties
│          │              │  │      log4j.properties
│          │              │  │      mybatis-config.xml
│          │              │  │
│          │              │  ├─task
│          │              │  │      BookProcessor.java
│          │              │  │
│          │              │  └─util
│          │              │          FileUtils.java
│          │              │          HttpUtils.java
│          │              │          MyBatisUtils.java
│          │              │
│          │              ├─dao
│          │              │      BookMapper.java
│          │              │      BookMapper.xml
│          │              │
│          │              ├─pojo
│          │              │      Book.java
│          │              │
│          │              └─service
│          │                  │  BookService.java
│          │                  │
│          │                  └─impl
│          │                          BookServiceImpl.java
│          │
│          └─resources
│              │  application.properties
│              │
│              ├─static
│              │  │  exist.txt
│              │  │
│              │  └─easyui
│              │      │  jquery.easyui.min.js
│              │      │  jquery.min.js
│              │      │
│              │      ├─css
│              │      │      default.css
│              │      │      demo.css
│              │      │
│              │      ├─js
│              │      │      outlook2.js
│              │      │      validateExtends.js
│              │      │
│              │      └─themes
|              |         |
│              │         |(略..)
│              │
|              |        
│              └─templates
│                      bookList.html
│                      intro.html
│                      main.html
│
└─demonstration-images
        BookCrawler-V3.0-bookList.PNG
        BookCrawler-V3.0-Intro.PNG
```

> `crawler`包存放的是`JDBookCrawler-v2.0`，既`3.0`使用了`2.0`的爬虫功能，其本身仅加入了页面可视化功能


### 运行指南
1. *crawler.sql : 数据库文件*
```
BookCrawler-v3.0/bookcrawler-v3.0/crawler.sql
```

2. *BookCrawlerTest.java : 爬虫启动程序*
```java
package pers.huangyuhui.bookcrawler.crawler;


import pers.huangyuhui.bookcrawler.crawler.task.BookProcessor;
import pers.huangyuhui.bookcrawler.crawler.util.FileUtils;

/**
 * @project: bookcrawler
 * @description: 爬虫测试程序
 * @author: 黄宇辉
 * @date: 7/11/2019-9:12 PM
 * @version: 2.0
 * @website: https://yubuntu0109.github.io/
 */
public class BookCrawlerTest {
    //指定图书关键字
    private static final String KEY_WORD = "网络爬虫";
    //指定页码数,每页可爬取三十条数据( 注:下一页的页码数为前一页的页码数加二 )
    private static final int END_PAGE_NUM = 2;
    private static final int CURRENT_PAGE_NUM = 1;
    //指定项目下存储书籍图片的文件夹路径
    private static final String IMAGE_PATH = FileUtils.getDirPath("/static/download/bookImage/");
    //指定JD商城书籍列表页面的链接
    private static final String URL = "https://search.jd.com/Search?keyword=" + KEY_WORD + "&enc=utf-8&page=";

    /**
     * @description: 启动爬虫程序
     * @date: 2019-07-15 4:09 PM
     */
    public static void main(String[] args) {
        new BookProcessor(URL, IMAGE_PATH, CURRENT_PAGE_NUM, END_PAGE_NUM).run();
    }
}
```

> 启动爬虫后其控制台输出的日志信息请参考 : [`JDBookCrawler v2.0`](https://yubuntu0109.github.io/2019/07/15/%E5%B0%8F%E7%88%AC%E8%99%AB-JDBookCrawler-V2-0/)

3. *BookcrawlerApplication.java : 项目启动类( 爬虫程序成功运行完成后，就可以启动该`springboot`项目啦，这操作不尴尬哈哈哈 ~ )*
```java
package pers.huangyuhui.bookcrawler;

import org.mybatis.spring.annotation.MapperScan;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

/**
 * @project: bookcrawler
 * @description: springboot项目启动类
 * @author: 黄宇辉
 * @date: 7/11/2019-9:16 PM
 * @version: 3.0
 * @website: https://yubuntu0109.github.io/
 */
@SpringBootApplication
@MapperScan("pers.huangyuhui.bookcrawler.dao") //扫描Mapper接口
public class BookcrawlerApplication {

    public static void main(String[] args) { SpringApplication.run(BookcrawlerApplication.class, args); }
}
```

4. *项目成功启动后，其项目主页，图书数据管理页如下图所示 :*

![ ](https://raw.githubusercontent.com/YUbuntu0109/Crawler-learning/master/BookCrawler-v3.0/demonstration-images/BookCrawler-V3.0-Intro.PNG)

![ ](https://raw.githubusercontent.com/YUbuntu0109/Crawler-learning/master/BookCrawler-v3.0/demonstration-images/BookCrawler-V3.0-bookList.PNG)

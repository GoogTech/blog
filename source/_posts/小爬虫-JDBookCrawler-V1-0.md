---
title: '小爬虫:JDBookCrawler-V1.0'
date: 2019-07-14 13:30:47
tags: [Java,HttpClient,Jsoup,MySQL,Crawler]
---

## 爬虫项目笔记 : JDBookCrawler-V1.0
*简介 : 最近在学习爬虫技术,浅薄学习`HttpClient`,`Jsoup`后,为了总结知识点既而写了这个小爬虫 : 爬取京东商城书籍数据. 程序已Psuh到GitHub : https://github.com/YUbuntu0109/Crawler-learning/tree/master/BookCrawler-v1.0/bookcrawler*


### 项目概述
#### 项目结构图
```
└─BookCrawler-v1.0
        └─bookcrawler
            │ 
            │  crawler.sql
            │  pom.xml
            │
            └─src
                └─main
                    ├─java
                    │  └─pers
                    │      └─huangyuhui
                    │          └─bookcrawler
                    │              │  BookCrawlerTest.java
                    │              │
                    │              ├─dao
                    │              │      BookDao.java
                    │              │
                    │              ├─pojo
                    │              │      Book.java
                    │              │      HttpHeader.java
                    │              │
                    │              ├─task
                    │              │      BookCrawler.java
                    │              │
                    │              └─util
                    │                      ConfigManager.java
                    │                      DBUtils.java
                    │                      HttpUtils.java
                    │
                    └─resources
                            db.properties
```


#### 程序运行指南
1. *crawler.sql : MySQL数据库文件*
```sql
BookCrawler-v1.0/bookcrawler/crawler.sql
```

2. *db.properties : 连接数据库的配置文件*
```
jdbc.driver.class=com.mysql.cj.jdbc.Driver
jdbc.connection.url=jdbc:mysql://localhost/crawler?useSSL=false&serverTimezone=UTC&allowPublicKeyRetrieval=true
jdbc.connection.username=x-x-x-x-x-x
jdbc.connection.password=x-x-x-x-x-x
```

3. *BookCrawlerTest.java : 爬虫启动程序*
```java
package pers.huangyuhui.bookcrawler;

import pers.huangyuhui.bookcrawler.pojo.HttpHeader;
import pers.huangyuhui.bookcrawler.task.BookCrawler;

/**
 * @project: BookCrawler
 * @description: 京东商城书籍爬虫v1.0-测试程序
 * @author: 黄宇辉
 * @date: 7/13/2019-5:57 PM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public class BookCrawlerTest {
    //自定义搜索关键字
    private static String KEY_WORD = "Java";
    //自定义页码数,每页可爬取三十条书籍数据
    private static int currentPageNum = 1;
    private static int endPageNum = 10;
    //自定义存储书籍图片的目录路径
    private static final String IMAGE_PATH = "D:\\BookCrawler\\download\\bookImage\\";
    //初始化书籍页面链接
    private static final String URL = "https://search.jd.com/Search?keyword=" + KEY_WORD + "&enc=utf-8&page=";
    //自定义请求头信息
    private static HttpHeader httpHeader = new HttpHeader();

    //Test
    public static void main(String[] args) {
        httpHeader.setUserAgent("Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/71.0.3578.80 Safari/537.36");//需指定用户代理哟
        new BookCrawler().parse(URL, IMAGE_PATH, httpHeader, currentPageNum, endPageNum);
    }
}
```


4. *运行程序`BookCrawlerTest.java`,下载的图书图片如下图所示 :*

![ ](小爬虫-JDBookCrawler-V1-0\BookCrawler-V1.0-BookImage.PNG)

*数据库中存储的数据如下图所示 :*

![ ](小爬虫-JDBookCrawler-V1-0\BookCrawler-V1.0-DB.PNG)


*控制台输出的日志信息如下 :*
```
[message]------>Begin to crawling this page : [https://search.jd.com/Search?keyword=Java&enc=utf-8&page=1]


\\\\\\\\\\第 [1] 本\\\\\\\\\\
Book{sku='12602672', name='Java从入门到项目实战（全程视频版）', price=87.8, author='李兴华 著', publishing='中国水利水电出版社', pubDate='出版时间：2019-06-01', imageName='fdbbf41f-f01f-42ff-a643-4510a6f4c7c3.jpg', bookUrl='https://item.jd.com/12602672.html', imageUrl='https://img12.360buyimg.com/n7/jfs/t1/37162/11/8870/130954/5ccfe770E9ce31151/74a5a5dcd83ecc09.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [2] 本\\\\\\\\\\
Book{sku='12555860', name='Java从入门到精通（第5版）', price=61.4, author='明日科技 著，javascript:void（0）; 注', publishing='清华大学出版社', pubDate='出版时间：2019-02-01', imageName='41a6c90b-a216-4927-9e3e-86fd0f61b0b1.jpg', bookUrl='https://item.jd.com/12555860.html', imageUrl='https://img10.360buyimg.com/n7/jfs/t1/26339/8/10661/124305/5c8af829E4470835f/99742c91174d3d7a.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [3] 本\\\\\\\\\\
Book{sku='10058164', name='Java编程思想（第4版） [thinking in java]', price=71.3, author='[美] Bruce Eckel 著，陈昊鹏 译', publishing='机械工业出版社', pubDate='出版时间：2007-06-01', imageName='b492c81a-2c88-4941-bc16-3949ecdd75a4.jpg', bookUrl='https://item.jd.com/10058164.html', imageUrl='https://img14.360buyimg.com/n7/jfs/t2191/111/699154754/198998/32d7bfe0/5624b582Nbc01af5b.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [4] 本\\\\\\\\\\
Book{sku='12037418', name='Java核心技术 卷I：基础知识（原书第10版）', price=95.2, author='[美] 凯 S.霍斯特曼（Cay S.Horstmann） 著', publishing='机械工业出版社', pubDate='出版时间：2016-09-01', imageName='9617963d-b39b-4599-9089-7ffa845c842d.jpg', bookUrl='https://item.jd.com/12037418.html', imageUrl='https://img13.360buyimg.com/n7/jfs/t3214/126/2309313357/292133/6c038027/57e09a03Nc57334e1.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [5] 本\\\\\\\\\\
Book{sku='12507084', name='Effective Java中文版（原书第3版）', price=94.0, author='约书亚·布洛克（Joshua Bloch） 著，俞黎敏 译', publishing='机械工业出版社', pubDate='出版时间：2019-01-01', imageName='8532dc5b-e0f0-4a71-918a-36e59383dd96.jpg', bookUrl='https://item.jd.com/12507084.html', imageUrl='https://img14.360buyimg.com/n7/jfs/t1/28526/24/4598/165510/5c3433c7Ea1da5694/eb0bb43a326e8709.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [6] 本\\\\\\\\\\
Book{sku='11252778', name='深入理解Java虚拟机：JVM高级特性与最佳实践（第2版）', price=52.1, author='周志明 著', publishing='机械工业出版社', pubDate='出版时间：2013-05-01', imageName='1d7bb79e-9d6a-46b6-a61b-0569422aacf3.jpg', bookUrl='https://item.jd.com/11252778.html', imageUrl='https://img13.360buyimg.com/n7/jfs/t6130/167/771989293/235186/608d0264/592bf167Naf49f7f6.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [7] 本\\\\\\\\\\
Book{sku='12163151', name='JavaWeb项目开发实战入门（全彩版）', price=49.4, author='明日科技（MingRi Soft） 著，明日科技 编', publishing='吉林大学出版社', pubDate='出版时间：2017-04-01', imageName='9920e677-10e8-47be-8c1c-2ddcbcb2730b.jpg', bookUrl='https://item.jd.com/12163151.html', imageUrl='https://img11.360buyimg.com/n7/jfs/t1/37054/1/12327/313637/5d036a5fE35f26e20/f365f2357456269f.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [8] 本\\\\\\\\\\
Book{sku='12185501', name='零基础学Java（全彩版）', price=49.4, author='明日科技（MingRi Soft） 著，明日科技 编', publishing='吉林大学出版社', pubDate='出版时间：2017-09-01', imageName='0f308d89-e600-493e-a0fa-03e07860293c.jpg', bookUrl='https://item.jd.com/12185501.html', imageUrl='https://img11.360buyimg.com/n7/jfs/t1/57972/5/2455/276693/5d036b6cEbb183907/b8c1845431ba6753.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [9] 本\\\\\\\\\\
Book{sku='12284606', name='阿里巴巴Java开发手册', price=33.3, author='杨冠宝 著', publishing='电子工业出版社', pubDate='出版时间：2018-01-01', imageName='3c6099c0-4ade-4122-abdd-a0dc7f8dcdd4.png', bookUrl='https://item.jd.com/12284606.html', imageUrl='https://img11.360buyimg.com/n7/jfs/t15166/324/2041049880/267178/69de6aaa/5a693fa1Nf13ae221.png'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [10] 本\\\\\\\\\\
Book{sku='12518025', name='疯狂Java讲义（第5版）（含DVD光盘一张）', price=132.1, author='李刚 著', publishing='电子工业出版社', pubDate='出版时间：2019-04-01', imageName='86e78e7e-8fa1-4d88-95e4-15074a4498fd.jpg', bookUrl='https://item.jd.com/12518025.html', imageUrl='https://img10.360buyimg.com/n7/jfs/t1/41570/5/1180/179278/5cc54479E7522cee3/d5b50e571ccc926e.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [11] 本\\\\\\\\\\
Book{sku='10100190', name='O'Reilly：Head First Java（中文版 第2版 涵盖Java5.0）', price=54.3, author='[美] 塞若（Sierra K.），[美] 贝茨（Bates B.） 著，O'Reilly Taiwan公司 译', publishing='中国电力出版社', pubDate='出版时间：2007-02-01', imageName='17a129cb-1bde-4bec-b7c1-500ba689b447.jpg', bookUrl='https://item.jd.com/10100190.html', imageUrl='https://img10.360buyimg.com/n7/jfs/t2680/274/3707696254/120035/6281369a/57986bbdN27e0e4fe.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [12] 本\\\\\\\\\\
Book{sku='11886254', name='数据结构与算法分析：Java语言描述（原书第3版）', price=54.5, author='[美] 马克·艾伦·维斯 著，陈越 译', publishing='机械工业出版社', pubDate='出版时间：2016-03-01', imageName='3f3c402b-cd3b-4213-8e87-630d3e511069.jpg', bookUrl='https://item.jd.com/11886254.html', imageUrl='https://img14.360buyimg.com/n7/jfs/t1897/55/2837612294/467469/83cd5b6/56f25c46Nc3b3c506.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [13] 本\\\\\\\\\\
Book{sku='12163091', name='Java项目开发实战入门（全彩版）', price=36.5, author='明日科技（MingRi Soft） 著', publishing='吉林大学出版社', pubDate='出版时间：2017-03-01', imageName='944f9dc8-c987-4c15-a866-5519f299ae97.jpg', bookUrl='https://item.jd.com/12163091.html', imageUrl='https://img11.360buyimg.com/n7/jfs/t1/78351/5/3554/283954/5d1c4d52E697fb1ee/53f88bb9ebbca1af.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [14] 本\\\\\\\\\\
Book{sku='12198563', name='Java核心技术卷II：高级特性（原书第10版）', price=111.2, author='[美] 凯 S. 霍斯特曼（Cay S. Horstmann） 著，陈昊鹏 译', publishing='机械工业出版社', pubDate='出版时间：2017-09-01', imageName='4280a987-84d7-421a-97b0-b3274b3a406b.jpg', bookUrl='https://item.jd.com/12198563.html', imageUrl='https://img13.360buyimg.com/n7/jfs/t8485/264/2434696739/100200/71e2437/59ccb6efN0d20c052.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [15] 本\\\\\\\\\\
Book{sku='12185937', name='Java精彩编程200例（全彩版）', price=56.4, author='明日科技（MingRi Soft） 著，明日科技 编', publishing='吉林大学出版社', pubDate='出版时间：2017-09-01', imageName='90314ef8-fc59-450d-9aa1-092f91dd2143.jpg', bookUrl='https://item.jd.com/12185937.html', imageUrl='https://img12.360buyimg.com/n7/jfs/t1/59101/8/2360/278076/5d03691aE30d418cf/ccc877ab66ae0b5e.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [16] 本\\\\\\\\\\
Book{sku='12445838', name='码出高效：Java开发手册', price=94.1, author='杨冠宝，高海慧 著', publishing='电子工业出版社', pubDate='出版时间：2018-10-01', imageName='7d99ffa3-0ccf-45af-b0dc-290657fc585a.jpg', bookUrl='https://item.jd.com/12445838.html', imageUrl='https://img13.360buyimg.com/n7/jfs/t30436/35/1494683199/69182/11fe5fb2/5ce20930N6d70a9f6.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [17] 本\\\\\\\\\\
Book{sku='12122571', name='Java EE互联网轻量级框架整合开发 SSM框架（Spring MVC+Spring+MyBatis）和Redis实现', price=94.0, author='杨开振，周吉文，梁华辉，谭茂华 著', publishing='电子工业出版社', pubDate='出版时间：2017-07-01', imageName='95e4d659-c74b-49e4-a07b-8269994ea6d4.jpg', bookUrl='https://item.jd.com/12122571.html', imageUrl='https://img11.360buyimg.com/n7/jfs/t1/19862/32/5482/345418/5c3fea85E5d15cbf3/95a87b49209f8f7e.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [18] 本\\\\\\\\\\
Book{sku='11917790', name='Java 8实战', price=62.4, author='[英] 厄马（Raoul-Gabriel Urma）[意] 著，陆明刚，劳佳 译', publishing='人民邮电出版社', pubDate='出版时间：2016-04-01', imageName='234259be-2795-466f-9d46-722d863a39b7.jpg', bookUrl='https://item.jd.com/11917790.html', imageUrl='https://img10.360buyimg.com/n7/jfs/t2395/169/2883784014/95060/83d0b6ec/571e49e0N5a93c2ee.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [19] 本\\\\\\\\\\
Book{sku='12368970', name='Java高并发编程详解：多线程与架构设计', price=70.3, author='汪文君 著 著', publishing='机械工业出版社', pubDate='出版时间：2018-05-01', imageName='fcd1f036-06cd-44d7-b8c8-a5490f3bd536.jpg', bookUrl='https://item.jd.com/12368970.html', imageUrl='https://img10.360buyimg.com/n7/jfs/t21481/161/1127754267/285318/7de60cc1/5b207165Nbd3f492d.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [20] 本\\\\\\\\\\
Book{sku='11740734', name='Java并发编程的艺术', price=56.1, author='方腾飞，魏鹏，程晓明 著', publishing='机械工业出版社', pubDate='出版时间：2015-07-01', imageName='fc23ab01-2df0-4e67-9bce-590ef47cfddd.jpg', bookUrl='https://item.jd.com/11740734.html', imageUrl='https://img14.360buyimg.com/n7/jfs/t1312/364/1104231705/156699/523dc84c/55b87a5eN09430825.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [21] 本\\\\\\\\\\
Book{sku='11894632', name='JavaEE开发的颠覆者：Spring Boot实战', price=84.6, author='汪云飞 著', publishing='电子工业出版社', pubDate='出版时间：2016-03-01', imageName='8a7de13b-d300-4971-abb5-98d6bb690b55.jpg', bookUrl='https://item.jd.com/11894632.html', imageUrl='https://img12.360buyimg.com/n7/jfs/t2107/53/2057024910/287539/276b11f9/56ef4a73Na00d39cb.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [22] 本\\\\\\\\\\
Book{sku='10922250', name='华章专业开发者丛书·Java并发编程实战', price=54.5, author='[美] Brian Goetz 等 著，童云兰 等 译', publishing='机械工业出版社', pubDate='出版时间：2012-02-01', imageName='839f0d9e-7454-4c86-8b92-640ec74667b7.jpg', bookUrl='https://item.jd.com/10922250.html', imageUrl='https://img10.360buyimg.com/n7/12706/18f0ebc9-287b-40a5-86f7-57b2b39c7ec8.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [23] 本\\\\\\\\\\
Book{sku='12408117', name='Java核心技术第10版 卷I+卷II 套装共2册', price=203.8, author='Cay，S.Horstmann 著', publishing='机械工业出版社', pubDate='出版时间：2018-08-01', imageName='399c00a7-c979-4461-80dc-c4352483032f.jpg', bookUrl='https://item.jd.com/12408117.html', imageUrl='https://img12.360buyimg.com/n7/jfs/t25795/255/981510338/254829/dadb19ce/5b84b3efN3a83e381.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [24] 本\\\\\\\\\\
Book{sku='10100236', name='O'Reilly：Head First设计模式（中文版）', price=67.4, author='[美] 弗里曼（Freeman E.） 等 著，UML China 编，OReilly Taiwan公司 译', publishing='中国电力出版社', pubDate='出版时间：2007-09-01', imageName='0e7e4526-10d0-4132-aa40-93674a3bf41b.jpg', bookUrl='https://item.jd.com/10100236.html', imageUrl='https://img11.360buyimg.com/n7/jfs/t2161/296/1756302188/97371/5fcfff8a/566f88e4Nd320f07b.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [25] 本\\\\\\\\\\
Book{sku='41559736446', name='Java解惑 （美）布洛赫', price=200.0, author='', publishing='宏田墨农图书专营店', pubDate='', imageName='7b0afdca-7b56-4877-b732-ce7cb86db948.jpg', bookUrl='https://item.jd.com/41559736446.html', imageUrl='https://img11.360buyimg.com/n7/jfs/t1/30722/19/6314/103860/5c8caf42E9e893400/8d73a86874877cc6.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [26] 本\\\\\\\\\\
Book{sku='10949745384', name='Java从入门到精通（第5版）（软件开发视频大讲堂）', price=29.9, author='明日科技 著', publishing='墨马图书旗舰店', pubDate='出版时间：2016-09-01', imageName='4cf16c8b-bb7d-46a2-b8d5-1d9196d89053.jpg', bookUrl='https://item.jd.com/10949745384.html', imageUrl='https://img14.360buyimg.com/n7/jfs/t1/60094/30/4064/161405/5d23f5a5Ef44d1a93/aefb557792d90dce.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [27] 本\\\\\\\\\\
Book{sku='22690417364', name='Java核心技术卷I基础知识+卷II高级特性（原书第10版） 华章图书 Java核心技术系', price=150.0, author='[美] 凯S.霍斯特曼(CayS.Horstmann 著', publishing='文轩网旗舰店', pubDate='出版时间：2016-09-01', imageName='8556cbc0-fa2f-4618-be05-61070dc7ca6a.png', bookUrl='https://item.jd.com/22690417364.html', imageUrl='https://img14.360buyimg.com/n7/jfs/t1/60410/21/4265/424620/5d26de24Eceb5bf01/883ea73c36bea683.png'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [28] 本\\\\\\\\\\
Book{sku='11899370', name='Spring实战（第4版）', price=78.3, author='[美] Craig Walls 沃尔斯 著，张卫滨 译', publishing='人民邮电出版社', pubDate='出版时间：2016-04-01', imageName='504ca7f0-af2d-4747-97b6-87aa91c5d8db.jpg', bookUrl='https://item.jd.com/11899370.html', imageUrl='https://img10.360buyimg.com/n7/jfs/t17503/334/529692453/390518/89ee87cb/5a93c36fNa6a46a43.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [29] 本\\\\\\\\\\
Book{sku='11098789', name='算法(第4版) [Algorithms Fourth Edition]', price=78.2, author='[美] Robert Sedgewick，[美] Kevin Wayne 著，谢路云 译', publishing='人民邮电出版社', pubDate='出版时间：2012-10-01', imageName='3f9b32ae-6c53-4edf-8cd1-9338959e5636.jpg', bookUrl='https://item.jd.com/11098789.html', imageUrl='https://img14.360buyimg.com/n7/jfs/t5779/357/2012363015/875007/26c3ba31/592bf163N035f1d2c.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [30] 本\\\\\\\\\\
Book{sku='12450812', name='Java并发编程之美', price=84.6, author='翟陆续，薛宾田 著', publishing='电子工业出版社', pubDate='出版时间：2018-10-01', imageName='66b237d1-5320-492d-9727-6e42f43a8556.jpg', bookUrl='https://item.jd.com/12450812.html', imageUrl='https://img12.360buyimg.com/n7/jfs/t1/1167/30/9347/118575/5baca2b1E5b8d2803/56dc8b9690fdebe8.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\


[message]------>This page has had crawled completly : [https://search.jd.com/Search?keyword=Java&enc=utf-8&page=]


[message]------>Begin to crawling this page : [https://search.jd.com/Search?keyword=Java&enc=utf-8&page=3]


\\\\\\\\\\第 [31] 本\\\\\\\\\\
Book{sku='24677303843', name='疯狂JAVA讲义(第四版)', price=78.5, author='李刚编著 著', publishing='文轩网旗舰店', pubDate='出版时间：2018-01-01', imageName='f04b7b6b-25d3-474c-847b-8f5350c0a59e.jpg', bookUrl='https://item.jd.com/24677303843.html', imageUrl='https://img13.360buyimg.com/n7/jfs/t28873/55/150261715/135795/7876803c/5be96452N71c20286.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [32] 本\\\\\\\\\\
Book{sku='13645330933', name='Java核心技术卷II+Java核心技术卷1 全2册 原书第10版 java入门书', price=149.0, author='', publishing='墨马图书旗舰店', pubDate='', imageName='aa0fc695-7e2b-4ea5-b734-0fefd30f39a4.jpg', bookUrl='https://item.jd.com/13645330933.html', imageUrl='https://img13.360buyimg.com/n7/jfs/t1/48548/24/4820/162629/5d26d982E08830568/e8f17b52a54361cd.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [33] 本\\\\\\\\\\
Book{sku='11355978', name='深入浅出Node.js', price=54.5, author='朴灵 著', publishing='人民邮电出版社', pubDate='出版时间：2013-12-01', imageName='64a61278-3781-4dec-95ae-2fe59d83654a.jpg', bookUrl='https://item.jd.com/11355978.html', imageUrl='https://img13.360buyimg.com/n7/jfs/t6094/107/710811867/382815/4d54717/592bf165N755a88f0.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [34] 本\\\\\\\\\\
Book{sku='12049209', name='Android Studio应用开发实战详解', price=60.7, author='王翠萍 著', publishing='人民邮电出版社', pubDate='出版时间：2017-02-01', imageName='44284270-dda4-404c-aa84-4a79de0beddb.jpg', bookUrl='https://item.jd.com/12049209.html', imageUrl='https://img14.360buyimg.com/n7/jfs/t4276/327/752840334/98356/931475ce/58b8f02dN377e2520.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [35] 本\\\\\\\\\\
Book{sku='12468732', name='Python从小白到大牛', price=78.3, author='关东升 著', publishing='清华大学出版社', pubDate='出版时间：2018-11-01', imageName='07623d02-a025-40ab-9f08-7178cc51f1f1.jpg', bookUrl='https://item.jd.com/12468732.html', imageUrl='https://img12.360buyimg.com/n7/jfs/t1/9499/9/2448/149643/5bd2be73E10148fe2/1922b5362c3bdea5.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [36] 本\\\\\\\\\\
Book{sku='12317007', name='Java设计模式（高等学校设计模式课程系列教材）', price=66.7, author='刘伟 著', publishing='清华大学出版社', pubDate='出版时间：2018-03-01', imageName='11367a22-ea04-4617-8362-0803db9449b1.jpg', bookUrl='https://item.jd.com/12317007.html', imageUrl='https://img12.360buyimg.com/n7/jfs/t18859/254/886040010/131423/b04938e9/5ab0be7aN8f05c0de.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [37] 本\\\\\\\\\\
Book{sku='50669504780', name='Spring Boot+Vue全栈开发实战 独立实现大型SPA应用书籍 基础语言Java编程思想开发', price=48.9, author='', publishing='兴阅图书专营店', pubDate='', imageName='f26e36d9-9497-49f7-bc72-c3098d9be58c.jpg', bookUrl='https://item.jd.com/50669504780.html', imageUrl='https://img10.360buyimg.com/n7/jfs/t1/55264/14/3054/31805/5d0d8696E6f200a00/01cdd32b88b79f97.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [38] 本\\\\\\\\\\
Book{sku='19297335', name='Thinking in Java Java编程思想 英文原版 [平装]', price=889.7, author='Bruce Eckel 著', publishing='Prentice Hall PTR', pubDate='出版时间：2006-02-20', imageName='f5be1014-17da-4b31-ab59-17abc76006ce.jpg', bookUrl='https://item.jd.com/19297335.html', imageUrl='https://img10.360buyimg.com/n7/g13/M03/10/0E/rBEhUlJbdQ4IAAAAAACM-xSPcewAAEGyAGhnvcAAI0T924.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [39] 本\\\\\\\\\\
Book{sku='1027811599', name='Java编程思想 华章图书 计算机科学丛书', price=69.8, author='[美] 埃克尔（BruceEckel）著陈吴鹏译 著', publishing='文轩网旗舰店', pubDate='出版时间：2007-06-01', imageName='d4e066e6-c4dc-4f52-b60a-f96f5e91947b.jpg', bookUrl='https://item.jd.com/1027811599.html', imageUrl='https://img14.360buyimg.com/n7/jfs/t1/30902/10/15584/84432/5cc28991E8b0697aa/4a7d9ef337fd3eae.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [40] 本\\\\\\\\\\
Book{sku='12621746', name='Spring响应式微服务：Spring Boot 2+Spring 5+Spring Cloud实战', price=71.3, author='郑天民 著', publishing='电子工业出版社', pubDate='出版时间：2019-06-01', imageName='178eb889-0b26-419d-889d-7cc4bb3d1e4a.jpg', bookUrl='https://item.jd.com/12621746.html', imageUrl='https://img11.360buyimg.com/n7/jfs/t1/49732/1/1266/87243/5cf091baE59493bdf/a489bfc9b8563c59.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [41] 本\\\\\\\\\\
Book{sku='12395281', name='NIO与Socket编程技术指南', price=68.3, author='高洪岩 著', publishing='机械工业出版社', pubDate='出版时间：2018-07-01', imageName='88e0e242-2ebd-42b0-89c3-7058c4239c13.jpg', bookUrl='https://item.jd.com/12395281.html', imageUrl='https://img11.360buyimg.com/n7/jfs/t22930/190/1223174459/96931/f0575eb0/5b56b9f2N4b4c8d13.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [42] 本\\\\\\\\\\
Book{sku='12610902', name='Java多线程编程核心技术（第2版）', price=68.3, author='高洪岩 著', publishing='机械工业出版社', pubDate='出版时间：2019-05-01', imageName='0ef7d3a3-9bfb-45d6-8920-ae4c77497123.jpg', bookUrl='https://item.jd.com/12610902.html', imageUrl='https://img12.360buyimg.com/n7/jfs/t29287/121/1505506965/99325/e56b418c/5ce25434N2dbc1b6c.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [43] 本\\\\\\\\\\
Book{sku='47862504172', name='java从入门到精通 上下册 Java编程入门指南 java编辑基础知识书籍 零基础自学JAVA语言', price=56.0, author='明日科技 著', publishing='华研外语官方旗舰店', pubDate='', imageName='8f08f39a-6a30-4b60-b9ad-01e72925b5cc.png', bookUrl='https://item.jd.com/47862504172.html', imageUrl='https://img12.360buyimg.com/n7/jfs/t10606/352/2945418656/423794/29037053/5cdd227dN01cf7974.png'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [44] 本\\\\\\\\\\
Book{sku='12403128', name='深入浅出Spring Boot 2.x', price=87.1, author='杨开振 著', publishing='人民邮电出版社', pubDate='出版时间：2018-08-01', imageName='48af1f52-6935-4a07-956b-eb7fa9dfa41f.jpg', bookUrl='https://item.jd.com/12403128.html', imageUrl='https://img13.360buyimg.com/n7/jfs/t22465/12/900897894/181167/6cdc011/5b46ddd0N0e99663d.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [45] 本\\\\\\\\\\
Book{sku='10603153', name='JavaScript DOM编程艺术（第2版）', price=38.7, author='[英] Jeremy Keith，[加] Jeffrey Sambells 著，杨涛 等 译', publishing='人民邮电出版社', pubDate='出版时间：2011-04-01', imageName='2282ee16-5405-460f-a0c7-e1930d164667.jpg', bookUrl='https://item.jd.com/10603153.html', imageUrl='https://img13.360buyimg.com/n7/jfs/t5914/15/800105189/215959/973eea1f/592bf164N56d3e3db.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [46] 本\\\\\\\\\\
Book{sku='12435255', name='分布式消息中间件实践', price=75.1, author='倪炜 著', publishing='电子工业出版社', pubDate='出版时间：2018-09-01', imageName='84a5d340-98c3-468a-b2a5-086f9d68fc0a.jpg', bookUrl='https://item.jd.com/12435255.html', imageUrl='https://img10.360buyimg.com/n7/jfs/t1/3450/19/9470/129555/5bac94d8E5620b17e/3a763b77711aab34.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [47] 本\\\\\\\\\\
Book{sku='10100583', name='怎样看110kV变电站典型二次回路图', price=14.0, author='郑新才，蒋剑 著', publishing='中国电力出版社', pubDate='出版时间：2009-10-01', imageName='a1a44a01-403b-41c2-bf6d-e3c8c5c15738.jpg', bookUrl='https://item.jd.com/10100583.html', imageUrl='https://img13.360buyimg.com/n7/19007/99f9dfce-38c8-47c3-b433-eb71edc9d734.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [48] 本\\\\\\\\\\
Book{sku='11449803', name='大型网站系统与Java中间件实践', price=61.8, author='曾宪杰 著', publishing='电子工业出版社', pubDate='出版时间：2014-04-01', imageName='3cc05539-3c8b-4d77-9964-0d327304ef5d.jpg', bookUrl='https://item.jd.com/11449803.html', imageUrl='https://img13.360buyimg.com/n7/g14/M01/00/1D/rBEhV1Ne_5MIAAAAAAkNeMWAC5AAAMwOQL4rRIACQ2Q252.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [49] 本\\\\\\\\\\
Book{sku='1027469792', name='Java并发编程实战', price=43.8, author='[美] 盖茨 著', publishing='文轩网旗舰店', pubDate='出版时间：2012-02-01', imageName='e1fb5b4d-8268-418b-a343-82b3383a3988.jpg', bookUrl='https://item.jd.com/1027469792.html', imageUrl='https://img12.360buyimg.com/n7/jfs/t1/5652/18/13839/60104/5bd93c37E3f0bfd91/87d0b3a61434181a.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [50] 本\\\\\\\\\\
Book{sku='12422624', name='Java基础入门', price=46.1, author='传智播客高教产品研发部 编', publishing='清华大学出版社', pubDate='出版时间：2014-05-01', imageName='235c88aa-e380-49ee-8f8e-5d6952c657ae.jpg', bookUrl='https://item.jd.com/12422624.html', imageUrl='https://img14.360buyimg.com/n7/jfs/t24940/109/1082655257/165480/27583a80/5b88fee7Ne45d1091.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [51] 本\\\\\\\\\\
Book{sku='12102001', name='Java Web从入门到精通（第2版）（配光盘）（软件开发视频大讲堂）', price=76.6, author='明日科技 著', publishing='清华大学出版社', pubDate='出版时间：2017-06-01', imageName='ebb5f9e7-2808-4aae-8880-81dd397b685a.jpg', bookUrl='https://item.jd.com/12102001.html', imageUrl='https://img11.360buyimg.com/n7/jfs/t5818/97/3776926155/98696/3b9619b4/59422c29N69528a65.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [52] 本\\\\\\\\\\
Book{sku='11676671', name='图灵程序设计丛书：你不知道的JavaScript（上卷） [You Don't Know JS:Scope & Closures]', price=38.7, author='[美] 辛普森（Kyle Simpson） 著，赵望野，梁杰 译', publishing='人民邮电出版社', pubDate='出版时间：2015-04-01', imageName='b97a4325-68fd-47ef-981c-30cf303066b2.jpg', bookUrl='https://item.jd.com/11676671.html', imageUrl='https://img11.360buyimg.com/n7/jfs/t6109/168/755288832/337238/a9c402b3/592bf16bN060edd40.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [53] 本\\\\\\\\\\
Book{sku='12458866', name='实战Java高并发程序设计（第2版）', price=84.6, author='葛一鸣 著', publishing='电子工业出版社', pubDate='出版时间：2018-10-01', imageName='0c867b6c-c8f6-434e-890e-4cdb5f3a53dd.jpg', bookUrl='https://item.jd.com/12458866.html', imageUrl='https://img11.360buyimg.com/n7/jfs/t1/9457/10/1147/146908/5bcd3149Ee45f979e/e5b85ad450838567.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [54] 本\\\\\\\\\\
Book{sku='12388762', name='Java语言程序设计与数据结构（基础篇）（原书第11版）', price=75.9, author='[美] 梁勇（Y. Daniel Liang） 著，戴开宇 复旦大学译 译', publishing='机械工业出版社', pubDate='出版时间：2018-06-01', imageName='279f89fc-d32b-4f6f-a8bc-a718d1987e23.jpg', bookUrl='https://item.jd.com/12388762.html', imageUrl='https://img12.360buyimg.com/n7/jfs/t22684/234/484637682/160167/5f280528/5b30be5cN7db0b1de.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [55] 本\\\\\\\\\\
Book{sku='41536196488', name='EFFECTIVE JAVA 3 中文版原书第三版', price=71.9, author='[美] 约书亚·布洛克（JoshuaBloch） 著', publishing='文轩网旗舰店', pubDate='出版时间：2019-01-01', imageName='a97f569d-7945-4394-a1ad-8841bef82e33.png', bookUrl='https://item.jd.com/41536196488.html', imageUrl='https://img13.360buyimg.com/n7/jfs/t1/40336/22/2855/347935/5cc2899aE1e0b25b1/c3f196fa8e0312fe.png'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [56] 本\\\\\\\\\\
Book{sku='12534456', name='互联网轻量级SSM框架解密：Spring、Spring MVC、MyBatis源码深度剖析', price=103.6, author='李艳鹏 等 著', publishing='电子工业出版社', pubDate='出版时间：2019-03-01', imageName='2403d1a6-7f64-43f4-8155-2dd0f1d2d7a2.jpg', bookUrl='https://item.jd.com/12534456.html', imageUrl='https://img11.360buyimg.com/n7/jfs/t1/16122/35/5881/158653/5c45966dE8b5c73ac/cb7b872dd995912e.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [57] 本\\\\\\\\\\
Book{sku='12587702', name='Java核心技术 卷I 基础知识 第11版 英文版 上下册', price=131.1, author='[美] 凯·S.，霍斯特曼（Cay，S.，Horstmann） 著', publishing='人民邮电出版社', pubDate='出版时间：2019-05-01', imageName='3737dbb3-7f40-4c6c-8012-ad863cd9374d.jpg', bookUrl='https://item.jd.com/12587702.html', imageUrl='https://img12.360buyimg.com/n7/jfs/t1/36138/24/1739/106284/5cb4114eEe575e257/9657767086f2e8fe.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [58] 本\\\\\\\\\\
Book{sku='12456376', name='正则指引（第2版）', price=84.6, author='余晟 著', publishing='电子工业出版社', pubDate='出版时间：2018-10-01', imageName='cb035e08-421e-4cc9-a02c-5a435f6e4722.jpg', bookUrl='https://item.jd.com/12456376.html', imageUrl='https://img11.360buyimg.com/n7/jfs/t27076/274/874525002/185922/be0e6dd2/5bbc4b37N07cffc59.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [59] 本\\\\\\\\\\
Book{sku='43069250577', name='正版6本SSH框架企业级应用+SSM轻量级框架应用+JavaWeb应用设计及实战+MySQL数据库应', price=201.45, author='肖睿 郭泰 王丁磊 著', publishing='品阅轩图书专营店', pubDate='出版时间：2018-01-01', imageName='21858fcc-b612-43e5-9958-f7ee7a53954c.png', bookUrl='https://item.jd.com/43069250577.html', imageUrl='https://img12.360buyimg.com/n7/jfs/t1/17642/6/12903/431603/5c9b4783E2c506db8/76b9707fa7879cb8.png'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [60] 本\\\\\\\\\\
Book{sku='12114302', name='Java基础案例教程', price=39.4, author='黑马程序员 著', publishing='人民邮电出版社', pubDate='出版时间：2017-01-01', imageName='a2be7356-6e6c-43b0-bdfd-13410558affa.jpg', bookUrl='https://item.jd.com/12114302.html', imageUrl='https://img12.360buyimg.com/n7/jfs/t24253/234/2303901363/171875/d03546c2/5b7bc8d5N8ca2ba51.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\


[message]------>This page has had crawled completly : [https://search.jd.com/Search?keyword=Java&enc=utf-8&page=]


[message]------>Begin to crawling this page : [https://search.jd.com/Search?keyword=Java&enc=utf-8&page=5]


\\\\\\\\\\第 [61] 本\\\\\\\\\\
Book{sku='22690417364', name='Java核心技术卷I基础知识+卷II高级特性（原书第10版） 华章图书 Java核心技术系', price=150.0, author='[美] 凯S.霍斯特曼(CayS.Horstmann 著', publishing='文轩网旗舰店', pubDate='出版时间：2016-09-01', imageName='95e9b54e-93c3-4e40-8d2c-ad991829daed.png', bookUrl='https://item.jd.com/22690417364.html', imageUrl='https://img14.360buyimg.com/n7/jfs/t1/60410/21/4265/424620/5d26de24Eceb5bf01/883ea73c36bea683.png'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [62] 本\\\\\\\\\\
Book{sku='12403477', name='Java开发详解（全彩版）', price=86.3, author='明日科技（MingRi Soft） 著，明日科技 编', publishing='吉林大学出版社', pubDate='出版时间：2018-07-01', imageName='0ce5dbcb-7750-407e-a685-21b9b9d497f5.jpg', bookUrl='https://item.jd.com/12403477.html', imageUrl='https://img12.360buyimg.com/n7/jfs/t1/44303/14/1165/281427/5cc53c63Eb79cabfb/a2a8c2f082c16238.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [63] 本\\\\\\\\\\
Book{sku='12019365', name='图解设计模式', price=62.4, author='[日] 结城浩 著，杨文轩 译', publishing='人民邮电出版社', pubDate='出版时间：2016-12-01', imageName='168fdcb7-0f62-48c9-99bd-a8a0cb91623b.jpg', bookUrl='https://item.jd.com/12019365.html', imageUrl='https://img10.360buyimg.com/n7/jfs/t3244/19/4701748027/371568/973000db/585358ebN2d6b6a66.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [64] 本\\\\\\\\\\
Book{sku='12113317', name='Three.js开发指南：WebGL的JavaScript 3D库（原书第2版）', price=51.2, author='[美] 乔斯·德克森（Jos Dirksen） 著，杨芬 译', publishing='机械工业出版社', pubDate='出版时间：2017-06-01', imageName='af99e3de-4081-42ac-9059-40984ca27234.jpg', bookUrl='https://item.jd.com/12113317.html', imageUrl='https://img12.360buyimg.com/n7/jfs/t6358/7/1544342065/111169/e51a6cb0/59536a88N6cdfbad6.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [65] 本\\\\\\\\\\
Book{sku='11890435', name='Java和Android开发学习指南 第2版', price=60.7, author='[加] 克尼亚万（Budi Kurniawan） 著，李强 译', publishing='人民邮电出版社', pubDate='出版时间：2016-03-01', imageName='2b9dd61c-21c3-4429-b27d-7ac98d77ba53.jpg', bookUrl='https://item.jd.com/11890435.html', imageUrl='https://img10.360buyimg.com/n7/jfs/t2320/312/2599966871/158930/ba4d65c2/56e6e180Nb900ed78.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [66] 本\\\\\\\\\\
Book{sku='12224904', name='Kotlin实战', price=84.6, author='[美] Dmitry Jemerov（德米特里·詹莫瑞福），[美] Svetlana Isakova（斯维特拉娜·伊凡诺沃） 著，覃宇，罗丽，李思阳，蒋扬海 译', publishing='电子工业出版社', pubDate='出版时间：2017-08-01', imageName='453bd598-1069-437c-a11e-4552191ae8a2.jpg', bookUrl='https://item.jd.com/12224904.html', imageUrl='https://img14.360buyimg.com/n7/jfs/t6061/329/6214493869/204427/b10fb30a/59715eb7N3e27da7c.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [67] 本\\\\\\\\\\
Book{sku='45822504367', name='Java语言的科学与艺术(国外计算机科学经典教材)9787302184416(美)罗伯茨,', price=198.0, author='美罗伯茨，付勇 著', publishing='正瀚图书专营店', pubDate='出版时间：2009-01-01', imageName='a9c28b69-bf07-430a-9e87-fd5850293919.jpg', bookUrl='https://item.jd.com/45822504367.html', imageUrl='https://img12.360buyimg.com/n7/jfs/t1/32613/36/13257/20975/5cb9ce81Eea6a06b9/4551fc7c020d0440.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [68] 本\\\\\\\\\\
Book{sku='12380383', name='疯狂Android讲义（Kotlin版）', price=102.6, author='李刚 著', publishing='电子工业出版社', pubDate='出版时间：2018-06-01', imageName='40bf6728-96ab-48a4-a672-e808a6ee7ad9.jpg', bookUrl='https://item.jd.com/12380383.html', imageUrl='https://img13.360buyimg.com/n7/jfs/t24124/92/502105589/496222/fcfb82ec/5b31ec57N2511feed.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [69] 本\\\\\\\\\\
Book{sku='12165317', name='图解Java多线程设计模式', price=70.3, author='结城浩 著，侯振龙，杨文轩 译', publishing='人民邮电出版社', pubDate='出版时间：2017-08-01', imageName='a403a845-c612-4dee-87f7-15bf91cf17ad.jpg', bookUrl='https://item.jd.com/12165317.html', imageUrl='https://img12.360buyimg.com/n7/jfs/t5797/85/9205318505/62242/28fec040/599a668aN07d69866.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [70] 本\\\\\\\\\\
Book{sku='41567918434', name='EFFECTIVE JAVA中文版(第3版)+深入理解JAVA虚拟机', price=143.7, author='[美] 约书亚·布洛克（JoshuaBloch） 著', publishing='文轩网旗舰店', pubDate='出版时间：2018-12-01', imageName='beff6a1e-5707-4c54-9d77-12708eba83bd.png', bookUrl='https://item.jd.com/41567918434.html', imageUrl='https://img14.360buyimg.com/n7/jfs/t1/20454/25/6745/402129/5c63df9eE5bc92a32/4a00f2502c9fd4e4.png'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [71] 本\\\\\\\\\\
Book{sku='12261787', name='疯狂Java讲义（第4版）', price=103.6, author='李刚 著', publishing='电子工业出版社', pubDate='出版时间：2018-01-01', imageName='09aab716-cd94-4d0f-942f-ca0697801225.jpg', bookUrl='https://item.jd.com/12261787.html', imageUrl='https://img12.360buyimg.com/n7/jfs/t1/332/12/6080/110939/5ba09fa6Eee7d7f85/4522ae0a5c8c5215.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [72] 本\\\\\\\\\\
Book{sku='11402560819', name='【包邮】码出高效 Java开发手册+阿里巴巴Java开发手册 java语言编程教程书籍', price=88.0, author='', publishing='蓝墨水图书专营店', pubDate='', imageName='21befded-3c1c-439e-be38-16664fc7e7c1.jpg', bookUrl='https://item.jd.com/11402560819.html', imageUrl='https://img14.360buyimg.com/n7/jfs/t22930/102/2058568140/110279/a6953560/5b723c2dN20cc6d14.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [73] 本\\\\\\\\\\
Book{sku='45585909326', name='分布式Java应用：基础与实践9787121109416林昊,电子工业出版社', price=159.0, author='林昊 著', publishing='正瀚图书专营店', pubDate='出版时间：2010-06-01', imageName='8ec5584c-ed9d-49a1-a100-64cd122cd31f.jpg', bookUrl='https://item.jd.com/45585909326.html', imageUrl='https://img11.360buyimg.com/n7/jfs/t1/32843/9/11797/24414/5cb5e4a5E7be8a7e6/9aa05b5495e6b72c.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [74] 本\\\\\\\\\\
Book{sku='10108619304', name='现货包邮 高教2019年版全国计算机等级考试二级教程 Java语言程序设计+公共基础知识 计算机等', price=49.6, author='', publishing='兰兴达图书专营店', pubDate='出版时间：2018-11-01', imageName='e06ad99e-756d-4379-9cd2-92f6d2ac5086.png', bookUrl='https://item.jd.com/10108619304.html', imageUrl='https://img14.360buyimg.com/n7/jfs/t29137/200/736353919/425760/5b1c7204/5bfcf17cNd5c644a0.png'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [75] 本\\\\\\\\\\
Book{sku='12408073', name='Java程序员面试算法宝典', price=66.2, author='猿媛之家 著', publishing='机械工业出版社', pubDate='出版时间：2018-08-01', imageName='d82ab71a-dc85-4718-84f7-7665628a12cd.jpg', bookUrl='https://item.jd.com/12408073.html', imageUrl='https://img13.360buyimg.com/n7/jfs/t24967/54/534149984/139359/aeaddb97/5b729cf9N18087ac4.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [76] 本\\\\\\\\\\
Book{sku='11407830', name='Java性能优化权威指南 [Java performance]', price=86.1, author='[美] Charlie Hunt，Binu John 著，柳飞，陆明刚 译', publishing='人民邮电出版社', pubDate='出版时间：2014-03-01', imageName='69d936a5-a577-43f3-9c6e-a6811aab3d00.jpg', bookUrl='https://item.jd.com/11407830.html', imageUrl='https://img10.360buyimg.com/n7/g13/M09/15/1E/rBEhUlMEatcIAAAAAAVMw6OwOPYAAIwSwIOpu0ABUzb978.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [77] 本\\\\\\\\\\
Book{sku='12256043', name='Java函数式编程', price=113.1, author='[法] Pierre-Yves，Saumont（皮埃尔-伊夫斯 索蒙特） 著，高清华 译', publishing='电子工业出版社', pubDate='出版时间：2017-11-01', imageName='5d7daffb-00cd-4d0b-811f-6738cf4454b6.jpg', bookUrl='https://item.jd.com/12256043.html', imageUrl='https://img13.360buyimg.com/n7/jfs/t1/15329/10/2381/206427/5c1c56ebE5534c0d2/12d15081720f73e4.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [78] 本\\\\\\\\\\
Book{sku='11703581', name='Java核心技术系列：Java虚拟机规范（Java SE 8版） [The Java Virtual Machine Specification Jave SE 8 Edition]', price=75.1, author='[美] 蒂姆·林霍尔姆（Tim，Lindholm），弗兰克·耶林（Frank Yellin），吉拉德·布拉查（Glad Bracha），亚历史斯·巴克利（Alex Buckley） 著，爱飞翔，周志明 等 译', publishing='机械工业出版社', pubDate='出版时间：2015-06-01', imageName='59fe9dd5-d484-4e01-9748-e6a6b880ab53.jpg', bookUrl='https://item.jd.com/11703581.html', imageUrl='https://img11.360buyimg.com/n7/jfs/t913/184/1143778297/240173/d94ec3e/55792c94N29049a7a.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [79] 本\\\\\\\\\\
Book{sku='26651591800', name='Java设计模式', price=50.9, author='刘伟编著 著', publishing='文轩网少儿专营店', pubDate='出版时间：2018-03-01', imageName='51c84da7-4a3f-4c0b-ae0c-a38ca956ece0.jpg', bookUrl='https://item.jd.com/26651591800.html', imageUrl='https://img10.360buyimg.com/n7/jfs/t1/4552/10/14657/47389/5bdc27ccE29d97349/ed944718c2ab28b1.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [80] 本\\\\\\\\\\
Book{sku='12375485', name='Java编程入门官方教程（第7版）', price=61.4, author='[美] Herbert Schildt 著，左雷 译', publishing='清华大学出版社', pubDate='出版时间：2018-06-01', imageName='bc0d9894-e697-43c6-87f2-66fc73f92436.jpg', bookUrl='https://item.jd.com/12375485.html', imageUrl='https://img10.360buyimg.com/n7/jfs/t23575/108/589507071/44408/81d8bdff/5b35a8d7N4a31ba40.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [81] 本\\\\\\\\\\
Book{sku='11099999', name='Java程序性能优化：让你的Java程序更快、更稳定', price=56.6, author='葛一鸣 等 著', publishing='清华大学出版社', pubDate='出版时间：2012-09-01', imageName='7c491cd2-f023-43d3-aa5f-8ccbe440b735.jpg', bookUrl='https://item.jd.com/11099999.html', imageUrl='https://img14.360buyimg.com/n7/g15/M09/19/11/rBEhWlKb234IAAAAAAQe--TPULkAAGMcAB9W-wABB8T163.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [82] 本\\\\\\\\\\
Book{sku='12573160', name='Java少儿编程', price=30.8, author='纳迪娅·阿梅西亚内·加西亚 著，李凡妮，姚均霖 译', publishing='人民邮电出版社', pubDate='出版时间：2019-03-01', imageName='d3bd567c-de8a-40ca-a35f-243a65fdcd56.jpg', bookUrl='https://item.jd.com/12573160.html', imageUrl='https://img10.360buyimg.com/n7/jfs/t1/21048/30/12451/112738/5c9854b6E61bc9360/6fc03b60e6a61bc1.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [83] 本\\\\\\\\\\
Book{sku='41551365134', name='机械工业：官方正版 Java核心技术(卷I)(基础知识)(原书第10版) Java核心', price=92.82, author='', publishing='新世纪图书专营店', pubDate='', imageName='50d4ad5f-016f-4846-88c9-6963fb00c6c1.jpg', bookUrl='https://item.jd.com/41551365134.html', imageUrl='https://img14.360buyimg.com/n7/jfs/t1/24495/4/6686/216823/5c627ea5E9a9b2e95/b1b972119a50c7f2.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [84] 本\\\\\\\\\\
Book{sku='12262844638', name='Netty权威指南 第2版 Java高性能NIO通信框架 大数据时代构建高可用分布式系统利', price=58.0, author='', publishing='润知天下图书专营店', pubDate='', imageName='15db5b12-232b-4245-82e8-427081701985.jpg', bookUrl='https://item.jd.com/12262844638.html', imageUrl='https://img13.360buyimg.com/n7/jfs/t5227/196/850978448/56451/abb128cb/590826faNe5a89553.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [85] 本\\\\\\\\\\
Book{sku='11550225525', name='Java核心技术 卷I：基础知识 原书第10版 华章图书 Java核心技术系列', price=75.8, author='[美] 凯S.霍斯特曼(CayS.Horstmann 著', publishing='文轩网旗舰店', pubDate='出版时间：2016-09-01', imageName='01f8205a-63b1-4e76-ae60-799ec0133cf4.jpg', bookUrl='https://item.jd.com/11550225525.html', imageUrl='https://img10.360buyimg.com/n7/jfs/t25831/30/2660146448/54798/20f01ee1/5bea76f9N86690ef9.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [86] 本\\\\\\\\\\
Book{sku='39003018140', name='2019年版全国计算机等级考试二级教程 Java语言程序设计 高等教育部指定计算机二级Java教材', price=32.9, author='考试中心 著', publishing='布克专营店', pubDate='出版时间：2018-11-01', imageName='44b93525-c82e-4a80-a634-45f16e43996f.jpg', bookUrl='https://item.jd.com/39003018140.html', imageUrl='https://img10.360buyimg.com/n7/jfs/t1/28943/5/2904/148459/5c21dc35E6968c918/c289064236c8a753.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [87] 本\\\\\\\\\\
Book{sku='12055718', name='21天学通Java 第7版', price=51.9, author='[美] 罗格斯·卡登海德（Rogers Cadenhead） 著，袁国忠 译', publishing='人民邮电出版社', pubDate='出版时间：2016-10-01', imageName='f7064a4d-5a7e-4cab-9170-e3ec04ad12a8.jpg', bookUrl='https://item.jd.com/12055718.html', imageUrl='https://img13.360buyimg.com/n7/jfs/t3412/193/424731803/105322/a75a8764/580a41acNa2d1f4eb.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [88] 本\\\\\\\\\\
Book{sku='41568057233', name='Java四大名著java编程思想+Effective java中文版（原书第3版）+Java核心技术', price=299.0, author='[美] 埃克尔（BruceEckel）著陈吴鹏译 著', publishing='文轩网旗舰店', pubDate='出版时间：2007-06-01', imageName='ed79915c-bbb4-4e0e-9f52-7c2abf4e0337.png', bookUrl='https://item.jd.com/41568057233.html', imageUrl='https://img13.360buyimg.com/n7/jfs/t1/53708/15/4673/540404/5d25480dE36392570/45eb35883bc01f7a.png'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [89] 本\\\\\\\\\\
Book{sku='12448711', name='智慧的疆界：从图灵机到人工智能 [The Boundaries of Intelligence from Turing Machine to Artificial Intelligence]', price=66.2, author='周志明 著', publishing='机械工业出版社', pubDate='出版时间：2018-10-01', imageName='1a1e9147-57c4-44f1-928b-0d7463a2f51d.jpg', bookUrl='https://item.jd.com/12448711.html', imageUrl='https://img11.360buyimg.com/n7/jfs/t1/8965/4/4114/198788/5bd9a09eE9e49a746/abbed9e17491f9b1.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [90] 本\\\\\\\\\\
Book{sku='12299018', name='Java编程的逻辑', price=94.1, author='马俊昌 著', publishing='机械工业出版社', pubDate='出版时间：2018-01-01', imageName='a59dfde8-aeda-4ca4-b926-4412758791f3.jpg', bookUrl='https://item.jd.com/12299018.html', imageUrl='https://img13.360buyimg.com/n7/jfs/t14494/344/2041325409/173602/8e9e46f2/5a66eb12N0a7be368.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\


[message]------>This page has had crawled completly : [https://search.jd.com/Search?keyword=Java&enc=utf-8&page=]


[message]------>Begin to crawling this page : [https://search.jd.com/Search?keyword=Java&enc=utf-8&page=7]


\\\\\\\\\\第 [91] 本\\\\\\\\\\
Book{sku='41708300430', name='JAVA WEB项目开发全程实录', price=60.6, author='明日科技 著', publishing='文轩网教育考试专营店', pubDate='出版时间：2018-11-01', imageName='705e226b-26eb-4a69-9d2c-2cbec83fe0bb.jpg', bookUrl='https://item.jd.com/41708300430.html', imageUrl='https://img10.360buyimg.com/n7/jfs/t1/18107/31/6867/67500/5c638437E1328cc93/9a01f5af5557116d.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [92] 本\\\\\\\\\\
Book{sku='44155570213', name='【包邮特价】Java核心技术　卷Ⅰ　基础知识（原书第10版）|64398', price=57.1, author='美 凯S 霍斯特曼Cay S Hor 著，周立新 译', publishing='互动创新图书专营店', pubDate='出版时间：2016-09-01', imageName='7e925184-9653-4893-bc71-8849b431e79e.jpg', bookUrl='https://item.jd.com/44155570213.html', imageUrl='https://img13.360buyimg.com/n7/jfs/t1/30658/29/7979/29956/5c9b1cdfEa1b61649/13241193de5b3b35.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [93] 本\\\\\\\\\\
Book{sku='12421187', name='Java核心技术及面试指南', price=45.7, author='金华，胡书敏，周国华，吴倍敏 著', publishing='北京大学出版社', pubDate='出版时间：2018-08-01', imageName='62eea26f-0558-498a-b522-80eedfce025b.jpg', bookUrl='https://item.jd.com/12421187.html', imageUrl='https://img12.360buyimg.com/n7/jfs/t23923/73/2580373102/326345/42f70f99/5b8851b1N940d8fbe.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [94] 本\\\\\\\\\\
Book{sku='12031620', name='自己动手写网络爬虫（修订版 配光盘）', price=33.3, author='罗刚 著', publishing='清华大学出版社', pubDate='出版时间：2016-08-01', imageName='2455f0ec-e755-455a-9437-88d07ad65616.jpg', bookUrl='https://item.jd.com/12031620.html', imageUrl='https://img10.360buyimg.com/n7/jfs/t3121/197/1971715943/328291/4767e03b/57d79b0fNb70e3a7c.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [95] 本\\\\\\\\\\
Book{sku='11737504', name='图解数据结构：使用Java', price=33.8, author='胡昭民 著', publishing='清华大学出版社', pubDate='出版时间：2015-07-01', imageName='17c6340d-cd63-41e5-8cd3-634c745b4ec7.jpg', bookUrl='https://item.jd.com/11737504.html', imageUrl='https://img14.360buyimg.com/n7/jfs/t1366/26/894983513/125597/ec5981c7/55b09e7bN587ffcd0.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [96] 本\\\\\\\\\\
Book{sku='44090780230', name='c语言从入门到精通+java从入门到精通 零基础电脑编程软件开发入门自学书籍 计算机c++', price=101.8, author='', publishing='友杰图书专营店', pubDate='', imageName='24af83e0-b334-43b8-b65a-d142c9011cfc.jpg', bookUrl='https://item.jd.com/44090780230.html', imageUrl='https://img10.360buyimg.com/n7/jfs/t1/24311/14/12881/471599/5c99d270E3bb044e6/ac74360ac178a4ab.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [97] 本\\\\\\\\\\
Book{sku='26667242534', name='Java核心技术卷II 高级特性 原书第10版 中文版 程序开发书籍', price=100.1, author='', publishing='华心图书专营店', pubDate='', imageName='bcac14aa-e677-48d4-b0cd-7e8ec92af494.jpg', bookUrl='https://item.jd.com/26667242534.html', imageUrl='https://img14.360buyimg.com/n7/jfs/t19762/65/1063598371/148796/6dfdad77/5abb550eNa1b11338.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [98] 本\\\\\\\\\\
Book{sku='11763003', name='实战Gradle', price=84.6, author='[美] Benjamin Muschko（本杰明·马斯可） 著，李建，杨柳，朱本威 译', publishing='电子工业出版社', pubDate='出版时间：2015-09-01', imageName='753b7522-f05d-4f77-9082-3a908c0ceb1b.jpg', bookUrl='https://item.jd.com/11763003.html', imageUrl='https://img13.360buyimg.com/n7/jfs/t1300/107/1072773516/494011/95eff86b/55ec0155Nf66f61fa.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [99] 本\\\\\\\\\\
Book{sku='12458778', name='自己动手实现Lua：虚拟机、编译器和标准库', price=85.4, author='张秀宏 著', publishing='机械工业出版社', pubDate='出版时间：2018-10-01', imageName='522a62ec-3ef4-408c-b3a7-5181da1bef08.jpg', bookUrl='https://item.jd.com/12458778.html', imageUrl='https://img13.360buyimg.com/n7/jfs/t27148/205/1034287519/111480/a295cbbd/5bbef1a5N7186e726.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [100] 本\\\\\\\\\\
Book{sku='12453426', name='精通Java并发编程 第2版', price=70.3, author='[西] 哈维尔·费尔南德斯·冈萨雷斯 著，唐富年 译', publishing='人民邮电出版社', pubDate='出版时间：2018-10-01', imageName='3d5d1f8b-0bc4-40b7-a4b9-5d67987e0f2e.jpg', bookUrl='https://item.jd.com/12453426.html', imageUrl='https://img11.360buyimg.com/n7/jfs/t27568/359/470391574/128058/1be8bc10/5baefd93N5266114d.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [101] 本\\\\\\\\\\
Book{sku='12513757', name='JVM G1源码分析和调优', price=84.6, author='彭成寒 著', publishing='机械工业出版社', pubDate='出版时间：2019-04-01', imageName='16417297-049e-43ad-a05f-5d948230ab8c.jpg', bookUrl='https://item.jd.com/12513757.html', imageUrl='https://img12.360buyimg.com/n7/jfs/t1/22145/5/15100/88532/5cad9a58E3526a54e/44766928ae9ae297.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [102] 本\\\\\\\\\\
Book{sku='42474709052', name='【套装2本】剑指Offer：名企面试官精讲典型编程题（第2版）+Java程序员面试笔试真题库', price=93.5, author='何海涛 著', publishing='华心图书专营店', pubDate='出版时间：2017-05-01', imageName='5a5f857d-1829-4a82-9158-62d5bf71c45c.jpg', bookUrl='https://item.jd.com/42474709052.html', imageUrl='https://img12.360buyimg.com/n7/jfs/t1/22055/7/9016/215731/5c7c79faE1362681f/a4b83345935d45bf.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [103] 本\\\\\\\\\\
Book{sku='36590815887', name='Java项目开发实战入门 Java从入门到精通 java语言程序设计java书籍教程java编程思想', price=38.8, author='', publishing='墨涵图书专营店', pubDate='出版时间：2006-08-01', imageName='9e3293e4-22b4-4ca6-9205-8ff150286aa2.jpg', bookUrl='https://item.jd.com/36590815887.html', imageUrl='https://img12.360buyimg.com/n7/jfs/t26281/43/1578788516/96461/80d4c4fd/5be5f2b5Nd48ea11e.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [104] 本\\\\\\\\\\
Book{sku='12465222', name='Java语言程序设计与数据结构（进阶篇 原书第11版） [Introduction to Java Programming and Data Structures Comprehensive Version，Eleventh Edition]', price=82.1, author='[美] 梁勇（Y.Daniel Liang） 著，戴开宇 译', publishing='机械工业出版社', pubDate='出版时间：2018-10-01', imageName='e94695f6-5fbb-44d1-9cf0-74dc9b8e0500.jpg', bookUrl='https://item.jd.com/12465222.html', imageUrl='https://img12.360buyimg.com/n7/jfs/t1/2121/6/13474/234872/5bd84120Eb021ec70/60e47b5f9b30c035.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [105] 本\\\\\\\\\\
Book{sku='12555764', name='Java编程技术大全（套装上下册）', price=104.7, author='魔乐科技（MLDN）软件实训中心 著', publishing='人民邮电出版社', pubDate='出版时间：2019-03-01', imageName='6c782009-a1c5-4032-804e-ce29956b9601.jpg', bookUrl='https://item.jd.com/12555764.html', imageUrl='https://img14.360buyimg.com/n7/jfs/t1/18518/38/9268/243891/5c7dd017E5cfb911f/572600bf9a76226c.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [106] 本\\\\\\\\\\
Book{sku='12086872', name='深度解析Java游戏服务器开发', price=75.1, author='何金成 著', publishing='电子工业出版社', pubDate='出版时间：2017-01-01', imageName='0204b1a3-dbf1-458a-b7b9-4639e6953975.jpg', bookUrl='https://item.jd.com/12086872.html', imageUrl='https://img12.360buyimg.com/n7/jfs/t3820/334/2296233492/141925/49d793ba/58573983N6fa2f9fd.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [107] 本\\\\\\\\\\
Book{sku='29984270108', name='数据结构与算法分析：Java语言描述 原书第3版', price=48.3, author='', publishing='芝麻开门图书专营店', pubDate='', imageName='e46ed4f3-3a1b-4f34-b4db-9e79780ce584.jpg', bookUrl='https://item.jd.com/29984270108.html', imageUrl='https://img13.360buyimg.com/n7/jfs/t23143/300/684699781/92720/e73c27cc/5b3d83e9Nc91e4db6.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [108] 本\\\\\\\\\\
Book{sku='12361134', name='Java项目开发全程实录（第4版）', price=67.0, author='明日科技 著', publishing='清华大学出版社', pubDate='出版时间：2018-05-01', imageName='a5e6a981-3291-4b7b-878b-e75c10a99063.jpg', bookUrl='https://item.jd.com/12361134.html', imageUrl='https://img14.360buyimg.com/n7/jfs/t16984/46/2480234140/146600/2ed350d4/5af947d8N672a939f.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [109] 本\\\\\\\\\\
Book{sku='43431775867', name='Java数据结构和算法 拉佛 9787508356440 中国电力出版社', price=119.0, author='', publishing='爱德雅图书专营店', pubDate='', imageName='dd0aebe4-d7c9-40b2-9366-854c335aed04.jpg', bookUrl='https://item.jd.com/43431775867.html', imageUrl='https://img12.360buyimg.com/n7/jfs/t1/29427/30/15617/108405/5cb16e25E59f4e362/0fc0d48027deb11c.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [110] 本\\\\\\\\\\
Book{sku='11670385', name='51CTO学院系列丛书·实战Java虚拟机：JVM故障诊断与性能优化', price=75.1, author='葛一鸣 著', publishing='电子工业出版社', pubDate='出版时间：2015-03-01', imageName='3c670922-e711-457c-a6b6-ff3382707068.jpg', bookUrl='https://item.jd.com/11670385.html', imageUrl='https://img10.360buyimg.com/n7/jfs/t766/291/1218388189/323388/a16b717e/55264f4aNff32a143.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [111] 本\\\\\\\\\\
Book{sku='12386639', name='Java JDK 9学习笔记', price=67.6, author='林信良 著', publishing='清华大学出版社', pubDate='出版时间：2018-06-01', imageName='38801c03-2afa-4a2a-a9bc-46e9c1e1e076.jpg', bookUrl='https://item.jd.com/12386639.html', imageUrl='https://img14.360buyimg.com/n7/jfs/t22300/323/2247756849/47752/911f1288/5b4ed9a8Nf6ca30a6.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [112] 本\\\\\\\\\\
Book{sku='43128267529', name='Effective Java中文版(原书第3版)', price=60.0, author='[美] 约书亚·布洛克（JoshuaBloch） 著', publishing='大石桥新华书店图书专营店', pubDate='出版时间：2019-01-01', imageName='67299383-d283-456a-be64-62e679599141.jpg', bookUrl='https://item.jd.com/43128267529.html', imageUrl='https://img14.360buyimg.com/n7/jfs/t1/35558/4/1965/53785/5cb47d51E8762dc13/56b053cf06c66419.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [113] 本\\\\\\\\\\
Book{sku='11800589', name='实战Java高并发程序设计', price=65.6, author='葛一鸣，郭超 著', publishing='电子工业出版社', pubDate='出版时间：2015-10-01', imageName='4ba3ae26-318c-425f-ad86-0faabec77a06.jpg', bookUrl='https://item.jd.com/11800589.html', imageUrl='https://img14.360buyimg.com/n7/jfs/t1978/210/1071750934/190027/becedfae/563b8251N19c01eb2.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [114] 本\\\\\\\\\\
Book{sku='12500208', name='区块链底层设计Java实战', price=65.6, author='牛冬 著', publishing='电子工业出版社', pubDate='出版时间：2018-12-01', imageName='69ca3774-0f97-4ab0-a495-d667da2e4668.jpg', bookUrl='https://item.jd.com/12500208.html', imageUrl='https://img13.360buyimg.com/n7/jfs/t1/7612/4/9370/392817/5c14a4a0E7bf4ce99/26ca34f21b1a403c.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [115] 本\\\\\\\\\\
Book{sku='45957612825', name='【套装2本】Java核心技术 卷I 基础知识 第10版 英文版 上下册 +Java核心技术 卷II', price=176.8, author='[美] 霍斯特曼 著', publishing='翔坤图书专营店', pubDate='出版时间：2016-06-01', imageName='13817e9b-295b-41d0-9e65-3f3bbd93018e.jpg', bookUrl='https://item.jd.com/45957612825.html', imageUrl='https://img10.360buyimg.com/n7/jfs/t1/38647/18/1231/164778/5cbc49bcEc6a00982/826536550103172e.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [116] 本\\\\\\\\\\
Book{sku='12384267', name='Java 9模块化开发：核心原则与实践', price=65.6, author='[荷] 桑德·马克 著，王净等译 译', publishing='机械工业出版社', pubDate='出版时间：2018-06-01', imageName='0f04fa92-edb2-4729-b370-554426c7b93c.jpg', bookUrl='https://item.jd.com/12384267.html', imageUrl='https://img12.360buyimg.com/n7/jfs/t24415/160/611982989/113145/19932968/5b3986d2N5531391e.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [117] 本\\\\\\\\\\
Book{sku='14710011813', name='Java核心技术 卷I 基础知识(原书第10版) 卷1 java编程思想 计算机基础', price=76.0, author='', publishing='墨马图书旗舰店', pubDate='', imageName='2760fab7-a4ff-4094-996a-1091f04b7893.jpg', bookUrl='https://item.jd.com/14710011813.html', imageUrl='https://img13.360buyimg.com/n7/jfs/t1/79921/9/4278/144326/5d26d9efEdeae7b82/08e9ce4c068da8d3.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [118] 本\\\\\\\\\\
Book{sku='10058149', name='Java编程思想（英文版）（第4版）', price=54.4, author='[美] 埃克尔 著', publishing='机械工业出版社', pubDate='出版时间：2007-04-01', imageName='5715c16b-f962-4082-a172-d10e04ff627e.jpg', bookUrl='https://item.jd.com/10058149.html', imageUrl='https://img14.360buyimg.com/n7/17310/9f5d4311-a88b-41e7-ae89-af45a1644b18.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [119] 本\\\\\\\\\\
Book{sku='10367718784', name='Java8实战', price=53.9, author='[英] 厄马（Raoul-GabrielUrma）[ 著', publishing='文轩网旗舰店', pubDate='出版时间：2016-04-01', imageName='a2cf2242-6f6b-4d80-be58-bfc3cab7909d.png', bookUrl='https://item.jd.com/10367718784.html', imageUrl='https://img14.360buyimg.com/n7/jfs/t1/72361/39/1775/483677/5d022ffdE244cda01/57c1b9cb4bddaad6.png'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [120] 本\\\\\\\\\\
Book{sku='12327466', name='轻量级Java EE企业应用实战（第5版）――Struts 2+Spring 5+Hiberna', price=121.6, author='李刚 著', publishing='电子工业出版社', pubDate='出版时间：2018-03-01', imageName='a823e637-7470-4a48-b216-5692c2eaa611.jpg', bookUrl='https://item.jd.com/12327466.html', imageUrl='https://img11.360buyimg.com/n7/jfs/t14452/269/2670034737/682465/a3b5a423/5ab38da3Ne4bbb41f.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\


[message]------>This page has had crawled completly : [https://search.jd.com/Search?keyword=Java&enc=utf-8&page=]


[message]------>Begin to crawling this page : [https://search.jd.com/Search?keyword=Java&enc=utf-8&page=9]


\\\\\\\\\\第 [121] 本\\\\\\\\\\
Book{sku='31163459184', name='包邮 [按需印刷]Java加密与解密的艺术(第2版)|3768927', price=89.0, author='梁栋 著', publishing='互动创新图书专营店', pubDate='出版时间：2014-01-01', imageName='c47edf98-43f3-42e2-b5f1-3710ce70e6f2.jpg', bookUrl='https://item.jd.com/31163459184.html', imageUrl='https://img14.360buyimg.com/n7/jfs/t1/21909/26/11649/68080/5c909f88Ef605f986/5b9231a93f654974.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [122] 本\\\\\\\\\\
Book{sku='12283376', name='名师讲坛：Java开发实战经典（第2版）（配光盘）', price=113.3, author='李兴华 著', publishing='清华大学出版社', pubDate='出版时间：2017-11-01', imageName='693dfc58-8ab4-4524-8d80-93e2ae16c910.jpg', bookUrl='https://item.jd.com/12283376.html', imageUrl='https://img11.360buyimg.com/n7/jfs/t16483/365/368938559/139663/9ea8e248/5a2f31d7N848ab555.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [123] 本\\\\\\\\\\
Book{sku='12165339', name='神经网络算法与实现 基于Java语言', price=51.9, author='[巴西] Fábio，M.，Soares，法比奥，Alan ... 著，范东来，封强 译', publishing='人民邮电出版社', pubDate='出版时间：2017-09-01', imageName='3f8cc308-7428-44fe-b5ce-5a27dbda9c27.jpg', bookUrl='https://item.jd.com/12165339.html', imageUrl='https://img14.360buyimg.com/n7/jfs/t7360/123/1084613304/153775/65018bcd/599a668bN381d917e.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [124] 本\\\\\\\\\\
Book{sku='12494412', name='Java数据分析指南', price=69.5, author='[美] 约翰·哈伯德（John，R.，Hubbard） 著，高蓉，李茂 译', publishing='人民邮电出版社', pubDate='出版时间：2018-12-01', imageName='9074ddd6-6227-4a5a-9de9-df07ac6e07b3.jpg', bookUrl='https://item.jd.com/12494412.html', imageUrl='https://img12.360buyimg.com/n7/jfs/t28843/357/839506568/135972/e33ce8a4/5c000f6dN105bb4ac.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [125] 本\\\\\\\\\\
Book{sku='12163883', name='Java测试驱动开发', price=38.7, author='[西] 维克多·法西克，阿列克斯·加西亚（Viktor，Farcic，Alex，Garcia） 著，袁国忠 译', publishing='人民邮电出版社', pubDate='出版时间：2017-08-01', imageName='6bb9f731-7ab8-4e3b-b3d7-8d1eef0cb172.jpg', bookUrl='https://item.jd.com/12163883.html', imageUrl='https://img13.360buyimg.com/n7/jfs/t7639/38/1116505980/90363/d639eb6e/599a4d66Nfbf73d28.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [126] 本\\\\\\\\\\
Book{sku='11269625', name='Java程序员修炼之道', price=61.4, author='[英] Benjamin J. Evans，[荷兰] Martijn Verburg 著，吴海星 译', publishing='人民邮电出版社', pubDate='出版时间：2013-07-01', imageName='3dd86b6c-e421-4f3d-af70-413c7d4b24f2.jpg', bookUrl='https://item.jd.com/11269625.html', imageUrl='https://img10.360buyimg.com/n7/g14/M06/03/05/rBEhVlHNNcwIAAAAAAJcIy_bbgQAAAnfAME2goAAlw7350.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [127] 本\\\\\\\\\\
Book{sku='41708300430', name='JAVA WEB项目开发全程实录', price=60.6, author='明日科技 著', publishing='文轩网教育考试专营店', pubDate='出版时间：2018-11-01', imageName='324faef0-8983-49a5-a3c2-3ca035b072b7.jpg', bookUrl='https://item.jd.com/41708300430.html', imageUrl='https://img10.360buyimg.com/n7/jfs/t1/18107/31/6867/67500/5c638437E1328cc93/9a01f5af5557116d.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [128] 本\\\\\\\\\\
Book{sku='32639062305', name='Java编程思想第4版 thinking in java中文版第四版 正版程序设计书籍', price=70.0, author='[美] Bruce Eckel 著', publishing='芝麻开门图书专营店', pubDate='出版时间：2007-05-31', imageName='d76a8245-4491-4e6d-b009-d8cff3917026.jpg', bookUrl='https://item.jd.com/32639062305.html', imageUrl='https://img10.360buyimg.com/n7/jfs/t1/1043/24/1131/246289/5b933671Ee8ebe0ad/d901647d53043923.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [129] 本\\\\\\\\\\
Book{sku='11948364', name='Java核心技术 卷I 基础知识 第10版 英文版 上下册', price=95.9, author='[美] 凯·S.霍斯特曼（Cay，S.，Horstmann） 著', publishing='人民邮电出版社', pubDate='出版时间：2016-06-01', imageName='2350b78c-e5fe-4a8c-b933-6f8a2c0901fe.jpg', bookUrl='https://item.jd.com/11948364.html', imageUrl='https://img14.360buyimg.com/n7/jfs/t2881/229/2259944638/80470/3ebe59d/575fac26Ne2b16fc5.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [130] 本\\\\\\\\\\
Book{sku='12542817', name='Three.js开发指南：基于WebGL和HTML5在网页上渲染3D图形和动画（原书第3版） [Learning Three.Js:Programming 3D Animations and Visualizations for the Web with HTML5 and WebGL, Third Edition]', price=94.1, author='[美] 乔斯·德克森（Jos Dirksen） 著，周翀，张薇 译', publishing='机械工业出版社', pubDate='出版时间：2019-06-01', imageName='feb3098e-3e43-4ee9-aa9c-289bbfd21a64.jpg', bookUrl='https://item.jd.com/12542817.html', imageUrl='https://img12.360buyimg.com/n7/jfs/t1/75130/23/3757/73942/5d1eedbaE198299c6/6dfac1198604db6e.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [131] 本\\\\\\\\\\
Book{sku='28450266477', name='数据结构与算法分析：Java语言描述 原书第3版', price=48.3, author='', publishing='静默时光图书专营店', pubDate='出版时间：2016-03-01', imageName='bb9a8c1d-7e6a-491f-9c5a-04e78472f38b.jpg', bookUrl='https://item.jd.com/28450266477.html', imageUrl='https://img12.360buyimg.com/n7/jfs/t19585/347/2645171682/187572/40555d8/5b03e16aNac0890bd.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [132] 本\\\\\\\\\\
Book{sku='13645344718', name='Java核心技术卷II+Java核心技术卷1 全2册 原书第10版 java入门书 编程开', price=145.0, author='', publishing='润知天下图书专营店', pubDate='', imageName='2b8d49e9-042a-401d-85eb-cd558896b3f5.jpg', bookUrl='https://item.jd.com/13645344718.html', imageUrl='https://img13.360buyimg.com/n7/jfs/t1/63469/11/4163/162629/5d26db87E0a8e66a2/ae1c80bd3ea2495d.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [133] 本\\\\\\\\\\
Book{sku='26667242534', name='Java核心技术卷II 高级特性 原书第10版 中文版 程序开发书籍', price=100.1, author='', publishing='华心图书专营店', pubDate='', imageName='5e489770-aab8-4dbc-a414-94283c0ccd96.jpg', bookUrl='https://item.jd.com/26667242534.html', imageUrl='https://img14.360buyimg.com/n7/jfs/t19762/65/1063598371/148796/6dfdad77/5abb550eNa1b11338.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [134] 本\\\\\\\\\\
Book{sku='12126544', name='Java Web程序设计任务教程', price=40.9, author='黑马程序员 著', publishing='人民邮电出版社', pubDate='出版时间：2017-01-01', imageName='5a7aafdc-4cc7-4601-9b5d-88939d935bba.jpg', bookUrl='https://item.jd.com/12126544.html', imageUrl='https://img14.360buyimg.com/n7/jfs/t26002/96/769192476/197740/a1cd6efc/5b7bb2a3N3b8bcc37.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [135] 本\\\\\\\\\\
Book{sku='11983163', name='Hibernate实战（第2版） [Java Persistence with Hibernate, Second Edition]', price=60.7, author='[德] Christian Bauer，[澳] Gavin King 著，蒲成 译', publishing='清华大学出版社', pubDate='出版时间：2016-09-01', imageName='d0a24200-f7d6-47a3-87eb-9ee2394fbaa1.jpg', bookUrl='https://item.jd.com/11983163.html', imageUrl='https://img13.360buyimg.com/n7/jfs/t3118/15/3813135988/133775/e61b97dc/57f8810bNf0dbe792.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [136] 本\\\\\\\\\\
Book{sku='11525283', name='Java特种兵（上册 附光盘）', price=75.1, author='谢宇 著', publishing='电子工业出版社', pubDate='出版时间：2014-09-01', imageName='a618b6b8-6b77-4c21-bb82-72fa17cea251.jpg', bookUrl='https://item.jd.com/11525283.html', imageUrl='https://img13.360buyimg.com/n7/jfs/t238/162/2222749565/209732/b2aa41a4/540ecdc1Nc17d12d4.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [137] 本\\\\\\\\\\
Book{sku='12179091', name='Java机器学习', price=38.7, author='[斯洛文尼亚] 博思蒂安·卡鲁扎（Bo·tjan Kalu·a） 著，武传海 译', publishing='人民邮电出版社', pubDate='出版时间：2017-09-01', imageName='49ac21ff-b817-4cba-84fd-79c8944e1a52.jpg', bookUrl='https://item.jd.com/12179091.html', imageUrl='https://img11.360buyimg.com/n7/jfs/t7627/330/2444106657/113904/4a53cc20/59afa68cN071ba978.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [138] 本\\\\\\\\\\
Book{sku='11615429', name='Java从入门到精通（第2版）', price=61.4, author='魔乐科技（MLDN）软件实训中心，张玉宏 编', publishing='人民邮电出版社', pubDate='出版时间：2015-03-01', imageName='4f968e6b-88f0-4711-ad92-23c63e3c9c70.jpg', bookUrl='https://item.jd.com/11615429.html', imageUrl='https://img14.360buyimg.com/n7/jfs/t1231/169/194210391/132178/44fd513d/55092239N36591404.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [139] 本\\\\\\\\\\
Book{sku='36590815887', name='Java项目开发实战入门 Java从入门到精通 java语言程序设计java书籍教程java编程思想', price=38.8, author='', publishing='墨涵图书专营店', pubDate='出版时间：2006-08-01', imageName='0280ebb8-d904-4b6b-ba57-11dffa3dc154.jpg', bookUrl='https://item.jd.com/36590815887.html', imageUrl='https://img12.360buyimg.com/n7/jfs/t26281/43/1578788516/96461/80d4c4fd/5be5f2b5Nd48ea11e.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [140] 本\\\\\\\\\\
Book{sku='12377547', name='Spring学习指南 第3版', price=78.3, author='[印度] J.夏尔马（J. Sharma），阿西施·萨林（Ashish Sarin） 著', publishing='人民邮电出版社', pubDate='出版时间：2018-07-01', imageName='94f56705-2555-45a8-8999-f442b257ad7b.jpg', bookUrl='https://item.jd.com/12377547.html', imageUrl='https://img12.360buyimg.com/n7/jfs/t23968/294/243413930/279875/124befec/5b2a19edNb9818bac.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [141] 本\\\\\\\\\\
Book{sku='12249634', name='疯狂前端开发讲义：jQuery+AngularJS+Bootstrap前端开发实战', price=75.1, author='李刚 著', publishing='电子工业出版社', pubDate='出版时间：2017-10-01', imageName='d33dfa19-bd2d-43e9-9b9e-fb9ba83ba561.jpg', bookUrl='https://item.jd.com/12249634.html', imageUrl='https://img14.360buyimg.com/n7/jfs/t10264/136/1233380306/324357/66dc456f/59de4605Na32313f3.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [142] 本\\\\\\\\\\
Book{sku='12073933', name='深入浅出React和Redux', price=54.5, author='程墨 著', publishing='机械工业出版社', pubDate='出版时间：2017-05-01', imageName='d7a9c238-1037-4ecf-b07a-e1e1b426da9c.jpg', bookUrl='https://item.jd.com/12073933.html', imageUrl='https://img13.360buyimg.com/n7/jfs/t5107/58/1653926146/128683/79be7ee8/5912e2fcNf9a839fc.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [143] 本\\\\\\\\\\
Book{sku='10189488174', name='大型网站技术架构+大型网站系统与Java中间件实践 （共两册）', price=90.6, author='李智慧 著', publishing='文轩网旗舰店', pubDate='出版时间：2013-11-01', imageName='ea44569b-1155-4bdc-a8ab-ed9711806d93.jpg', bookUrl='https://item.jd.com/10189488174.html', imageUrl='https://img14.360buyimg.com/n7/jfs/t19390/55/1480670900/71578/c2bffa92/5acb4125N250fc512.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [144] 本\\\\\\\\\\
Book{sku='48468755934', name='', price=3698.0, author='', publishing='佳沃', pubDate='', imageName='17824a12-db77-4eae-9bba-fcd11bf03ee9.jpg', bookUrl='https://item.jd.com/48468755934.html', imageUrl='https://img14.360buyimg.com/n7/jfs/t1/72048/34/294/146388/5ce7a96eEf35c4cd5/12660ede94253ebd.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [145] 本\\\\\\\\\\
Book{sku='43431775867', name='Java数据结构和算法 拉佛 9787508356440 中国电力出版社', price=119.0, author='', publishing='爱德雅图书专营店', pubDate='', imageName='469c5af0-a181-42bd-8ce8-c7218b07593e.jpg', bookUrl='https://item.jd.com/43431775867.html', imageUrl='https://img12.360buyimg.com/n7/jfs/t1/29427/30/15617/108405/5cb16e25E59f4e362/0fc0d48027deb11c.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [146] 本\\\\\\\\\\
Book{sku='11972824', name='数据结构与算法经典问题解析：Java语言描述(原书第2版）', price=62.4, author='[印度] 纳拉西姆哈·卡鲁曼希（Narasimha Karumanchi） 著，骆嘉伟 译', publishing='机械工业出版社', pubDate='出版时间：2016-06-01', imageName='50dd85ab-ebfa-44df-826f-493c1c870fac.jpg', bookUrl='https://item.jd.com/11972824.html', imageUrl='https://img14.360buyimg.com/n7/jfs/t2827/53/3285584017/111648/dbb7962c/5787050dN0c9428ae.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [147] 本\\\\\\\\\\
Book{sku='12068611', name='Java 2实用教程（第5版）实验指导与习题解答/高等学校Java课程系列教材', price=25.0, author='张跃平，耿祥义 著', publishing='清华大学出版社', pubDate='出版时间：2017-04-01', imageName='c7d82418-2711-4ab9-9f25-fb4c3fd34e6d.jpg', bookUrl='https://item.jd.com/12068611.html', imageUrl='https://img11.360buyimg.com/n7/jfs/t5146/32/423649499/74201/57f02580/58ff0b5cNd33fce0e.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [148] 本\\\\\\\\\\
Book{sku='12534924', name='Java面向对象思想与程序设计', price=59.3, author='刘彦君，张仁伟，满志强 著', publishing='人民邮电出版社', pubDate='出版时间：2018-11-01', imageName='d237c39a-e5be-4b33-b660-66ea3993b515.jpg', bookUrl='https://item.jd.com/12534924.html', imageUrl='https://img14.360buyimg.com/n7/jfs/t1/20377/40/6204/204310/5c49136aE717954ee/325c34e9ba230448.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [149] 本\\\\\\\\\\
Book{sku='18384142316', name='Java核心技术卷II：高级特性 原书第10版 华章图书 Java核心技术系列', price=87.8, author='[美] 凯S.霍斯特曼(CayS.Horstmann 著', publishing='文轩网旗舰店', pubDate='出版时间：2017-09-01', imageName='e8ab647d-bb8c-403b-819b-1861c0d6e5e8.jpg', bookUrl='https://item.jd.com/18384142316.html', imageUrl='https://img11.360buyimg.com/n7/jfs/t1/2608/16/7182/57817/5ba4ce62Ea982f0a9/90b6abd0629c209f.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




\\\\\\\\\\第 [150] 本\\\\\\\\\\
Book{sku='12167904', name='Java大学实用教程（第4版）', price=31.7, author='耿祥义 著', publishing='电子工业出版社', pubDate='出版时间：2017-03-01', imageName='4ceac15b-2d10-4161-868a-a3b098c06837.jpg', bookUrl='https://item.jd.com/12167904.html', imageUrl='https://img14.360buyimg.com/n7/jfs/t4663/293/1745170178/76878/a2d78fc0/58e51a8aN76bad3cb.jpg'}
[message]------>Data was added to the database successfully ヾ(◍°∇°◍)ﾉﾞ
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\


[message]------>This page has had crawled completly : [https://search.jd.com/Search?keyword=Java&enc=utf-8&page=]


Process finished with exit code 0
```
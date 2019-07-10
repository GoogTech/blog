---
title: Java爬虫之Jsoup
date: 2019-07-10 18:55:14
tags: [Java,Jsoup]
---

## 学习笔记 : Java爬虫之Jsoup(Java HTML Parser)
*jsoup is a Java library for working with real-world HTML. It provides a very convenient API for extracting and manipulating data, using the best of DOM, CSS, and jquery-like methods.*

*👍 jsoup Cookbook : https://www.open-open.com/jsoup/*


### 爬虫实现
*使用Jsoup解析不同数据的方式,示例程序如下 :*
```java
package pers.huangyuhui.crawler.Jsoup_Demo;

import org.apache.commons.io.FileUtils;
import org.jsoup.Jsoup;
import org.jsoup.nodes.Document;

import java.io.File;
import java.io.IOException;

/**
 * @project: crawler_learning
 * @description: 学习使用Jsoup
 * @author: 黄宇辉
 * @date: 7/8/2019-9:34 PM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public class JsoupTest {

    public static void main(String[] args) throws IOException {
        urlTest();
        stringTest();
        fileTest();
    }

    // TODO: 7/8/2019 解析URL:获取网页title标签内容
    private static void urlTest() throws IOException {

        //解析URL地址并设置请求信息,获取Document对象
        Document document = Jsoup.connect("https://www.bilibili.com/")
                .timeout(1000)
                .userAgent("x-x-x-x-x-x")
                .get();

        System.out.println(document.title());
    }

    // TODO: 7/9/2019 解析字符串:获取字符串中title标签内容
    private static void stringTest() throws IOException {
        //使用文件工具类读取文件,获取字符串
        String content = FileUtils.readFileToString(new File("C:/Users/Administrator/Desktop/html.html"), "gbk");
        //解析字符串
        Document doc = Jsoup.parse(content);
        System.out.println(doc.getElementsByTag("title").first().text());
    }

    // TODO: 7/9/2019 解析文件:获取文件中title标签内容
    private static void fileTest() throws IOException {
        //解析文件
        Document doc = Jsoup.parse(new File("C:/Users/Administrator/Desktop/html.html"), "gbk");
        System.out.println(doc.title());
    }

}
```


### Jsoup DOM
*学习使用Jsoup-DOM获取元素及其中数据,示例程序如下 :*
```java
package pers.huangyuhui.crawler.Jsoup_DOM;

import org.jsoup.Jsoup;
import org.jsoup.nodes.Document;
import org.jsoup.nodes.Element;
import org.jsoup.select.Elements;

import java.io.IOException;

/**
 * @project: crawler_learning
 * @description: 学习使用Jsoup-DOM获取元素及其中数据
 * @author: 黄宇辉
 * @date: 7/9/2019-8:18 AM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public class ElementTest {

    public static void main(String[] args) {
        try {
            /*
                解析url并设置请求信息,获取Document对象
             */
            Document doc = Jsoup.connect("https://yubuntu0109.github.io/")
                    .timeout(5000)
                    .userAgent("x-x-x-x-x-x")
                    .get();

            /*
                根据Document对象获取元素
             */
            //1:通过id获取元素:获取博客头标题
            Element element = doc.getElementById("header");
            System.out.println(element.text());

            //2:通过tag获取元素:获取博客文章标题
            Elements elements = doc.getElementsByTag("h3");
            for (Element e : elements) {
                System.out.println(e.text());
            }
            //3:通过class获取元素:获取博客文章简介
            Elements elements2 = doc.getElementsByClass("post-content");
            for (Element e : elements2) {
                System.out.println(e.text() + "\n");
            }
            //4:通过attribute获取元素:获取博客文章发布时间
            Elements elements3 = doc.getElementsByAttribute("datetime");
            for (Element e : elements3) {
                System.out.println(e.text());
            }
            //4-2:通过属性名加属性值筛选元素:获取博客文章标题
            Elements elements4 = doc.getElementsByAttributeValue("itemprop", "name");
            for (Element e : elements4) {
                System.out.println(e.text());
            }


            /*
                获取元素中的数据
             */
            Element e = doc.getElementById("header");
            //1:从元素中获取id
            System.out.println(e.id());
            //2:从元素中获取文本内容text
            System.out.println(e.text());
            //3:从元素中获取className
            System.out.println(e.className());
            //4:从元素中获取属性的值attr
            System.out.println(e.attr("id"));
            //5:从元素中获取所有属性attributes
            System.out.println(e.attributes().toString());
            // ······

        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```


### Jsoup Selector
*学习使用Jsoup选择器,示例程序如下 :*
```java
package pers.huangyuhui.crawler.Jsoup_Selector;

import org.jsoup.Jsoup;
import org.jsoup.nodes.Document;
import org.jsoup.nodes.Element;
import org.jsoup.select.Elements;

import java.io.IOException;

/**
 * @project: crawler_learning
 * @description: 学习使用Jsoup选择器
 * @author: 黄宇辉
 * @date: 7/9/2019-10:29 AM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public class SelectorTest {

    public static void main(String[] args) throws IOException {
        /*
            解析url并设置请求信息,获取Document对象
         */
        Document doc = Jsoup.connect("https://yubuntu0109.github.io/")
                .timeout(5000)
                .userAgent("x-x-x-x-x-x")
                .get();

        /*
            Jsoup选择器的使用方式
         */
        //el#id : 获取博客头标题
        System.out.println(doc.select("header#header").first().text());

        //el.class : 获取博客的导航标签
        Elements elements = doc.select("ul.nav");
        for (Element e : elements) {
            System.out.println(e.text());
        }

        //el[attr] : 获取博客文章标题
        Elements elements2 = doc.select("h3[itemprop]");
        for (Element e : elements2) {
            System.out.println(e.text());
        }

        //.ancestor child : 查询某个元素的下个子元素,获取博客导航标签
        Elements elements3 = doc.select(".nav li");
        for (Element e : elements3) {
            System.out.println(e.text());
        }

        //parent > child : 查询某个父元素下的直接子元素,获取博客文章链接的href值
        Elements elements4 = doc.select(".post-title > a");
        for (Element e : elements4) {
            System.out.println(e.attr("href"));
        }

        //parenet > * : 查询某个父元素下所有直接子元素,获取博客文章标题
        Elements elements5 = doc.select(".post-title > *");
        for (Element e : elements5) {
            System.out.println(e.text());
        }

    }
}
```
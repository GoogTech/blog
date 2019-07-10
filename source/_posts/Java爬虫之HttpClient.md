---
title: Java爬虫之HttpClient
date: 2019-07-10 18:54:55
tags: [Java,HttpClient]
---

## 学习笔记 : Java爬虫之HttpClient
*简介 : `HttpClient`是Apache Jakarta Common下的子项目,用于提供高效的,功能丰富的支持HTTP协议的客户编程工具包,其主要功能如下:*

1. *实现了所有HTTP的方法 : GET,POST,PUT,HEAD ..*
2. *支持自动重定向*
3. *支持HTTPS协议*
4. *支持代理服务器*


### 实现爬虫
*传统实现 : 爬取静态网页*
```java
package pers.huangyuhui.crawler.HttpClient_Demo;

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.net.URL;
import java.nio.charset.StandardCharsets;

/**
 * @project: crawler_learning
 * @description: 下载网页的基本方法:使用java.net.URL
 * @author: 黄宇辉
 * @date: 7/8/2019-11:16 AM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public class BasicCrawler {

    public static void main(String[] args) throws IOException {
        URL pageURL = new URL("https://yubuntu0109.github.io/");
        //创建数据流
        BufferedReader reader = new BufferedReader(new InputStreamReader(pageURL.openStream(), StandardCharsets.UTF_8));
        //获取网页内容
        String line;
        StringBuilder pageBuffer = new StringBuilder();
        while ((line = reader.readLine()) != null) {
            pageBuffer.append(line);
        }
        //释放资源
        reader.close();
        System.out.println(pageBuffer.toString());
    }
}
```

*HttpClient实现 : 爬取静态网页*
```java
package pers.huangyuhui.crawler.HttpClient_Demo;

import org.apache.http.HttpEntity;
import org.apache.http.client.methods.CloseableHttpResponse;
import org.apache.http.client.methods.HttpGet;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClients;
import org.apache.http.util.EntityUtils;

import java.io.IOException;

/**
 * @project: crawler_learning
 * @description: 下载网页的基本方法:使用HttpClient
 * @author: 黄宇辉
 * @date: 7/2/2019-10:42 PM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public class HttpClientCrawler {

    public static void main(String[] args) {

        //指定待爬取的网页链接并设置Header信息
        HttpGet httpGet = new HttpGet("https://www.bilibili.com/");
        httpGet.setHeader("User-Agent", "xxxxxx");

        //创建HttpClient对象
        try (CloseableHttpClient httpClient = HttpClients.createDefault()) {
            //发起请求,获取响应
            try (CloseableHttpResponse response = httpClient.execute(httpGet)) {
                //解析响应,获取数据
                if (response.getStatusLine().getStatusCode() == 200) {
                    HttpEntity httpEntity = response.getEntity(); //获取响应体
                    String content = EntityUtils.toString(httpEntity, "utf-8");//获取静态页面

                    System.out.println(content);
                }
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```



### GET请求
*无参GET请求 : 爬取B站静态网页*
```java
package pers.huangyuhui.crawler.HttpClient_Get;

import org.apache.http.HttpEntity;
import org.apache.http.client.methods.CloseableHttpResponse;
import org.apache.http.client.methods.HttpGet;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClients;
import org.apache.http.util.EntityUtils;

import java.io.IOException;

/**
 * @project: crawler_learning
 * @description: 学习使用无参HttpGet():爬取B站静态网页
 * @author: 黄宇辉
 * @date: 7/8/2019-12:26 PM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public class GetTest {

    public static void main(String[] args) {
        //指定待爬取网页的链接并设置Header信息
        HttpGet httpGet = new HttpGet("https://www.bilibili.com/");
        httpGet.setHeader("User-Agent", "x-x-x-x-x-x");

        //创建HttpClient对象
        try (CloseableHttpClient httpClient = HttpClients.createDefault()) {
            //发起请求,获取响应
            try (CloseableHttpResponse response = httpClient.execute(httpGet)) {
                //解析响应,获取数据
                if (response.getStatusLine().getStatusCode() == 200) {
                    HttpEntity httpEntity = response.getEntity(); //获取响应体
                    String content = EntityUtils.toString(httpEntity, "utf-8");//获取静态页面
                    System.out.println(content);
                }
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

*有参GET请求 : 爬取B站中搜索关键字为jsoup的网页内容*
```java
package pers.huangyuhui.crawler.HttpClient_Get;

import org.apache.http.HttpEntity;
import org.apache.http.client.methods.CloseableHttpResponse;
import org.apache.http.client.methods.HttpGet;
import org.apache.http.client.utils.URIBuilder;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClients;
import org.apache.http.util.EntityUtils;

import java.io.IOException;
import java.net.URISyntaxException;

/**
 * @project: crawler_learning
 * @description: 学习使用有参HttpGet(URI uri):爬取B站中搜索关键字为jsoup的网页内容
 * @author: 黄宇辉
 * @date: 7/8/2019-1:12 PM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public class GetParamTest {

    public static void main(String[] args) throws URISyntaxException, IOException {

        //设置HttpGet参数
        URIBuilder uriBuilder = new URIBuilder("https://search.bilibili.com/all");
        uriBuilder.setParameter("keyword", "jsoup");
        //指定待爬取的网页链接并设置Header信息
        HttpGet httpGet = new HttpGet(uriBuilder.build());
        httpGet.setHeader("User-Agent", "x-x-x-x-x-x");
        //创建HttpClient对象
        CloseableHttpClient httpClient = HttpClients.createDefault();
        //发起请求,获取响应
        CloseableHttpResponse response = httpClient.execute(httpGet);
        //解析响应,获取数据
        if (response.getStatusLine().getStatusCode() == 200) {
            HttpEntity httpEntity = response.getEntity(); //获取响应体
            String content = EntityUtils.toString(httpEntity, "utf-8");//获取静态页面
            System.out.println(content);
        }
        //释放资源
        response.close();
        httpClient.close();
    }

}
```



### POST请求
*无参POST请求 : 爬取CSDN静态网页*
```java
package pers.huangyuhui.crawler.HttpClient_Post;

import org.apache.http.HttpEntity;
import org.apache.http.client.methods.CloseableHttpResponse;
import org.apache.http.client.methods.HttpGet;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClients;
import org.apache.http.util.EntityUtils;

import java.io.IOException;

/**
 * @project: crawler_learning
 * @description: 学习使用无参HttpPost():爬取CSDN静态网页
 * @author: 黄宇辉
 * @date: 7/8/2019-12:26 PM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public class PostTest {

    public static void main(String[] args) {

        //指定待爬取的网页并设置Header信息
        HttpPost httpPost = new HttpPost("https://www.csdn.net/");
        httpPost.setHeader("User-Agent", "x-x-x-x-x-x");

        //创建HttpClient对象
        try (CloseableHttpClient httpClient = HttpClients.createDefault()) {
            //发起请求,获取响应
            try (CloseableHttpResponse response = httpClient.execute(httpPost)) {
                //解析响应,获取数据
                if (response.getStatusLine().getStatusCode() == 200) {
                    HttpEntity httpEntity = response.getEntity(); //获取响应体
                    String content = EntityUtils.toString(httpEntity, "utf-8");//获取静态页面
                    System.out.println(content);
                }
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

*有参POST请求 : 爬取B站中搜索关键字为jsoup的网页内容(注:其并不支持POST,既只做演示)*
```java
package pers.huangyuhui.crawler.HttpClient_Post;

import org.apache.http.HttpEntity;
import org.apache.http.NameValuePair;
import org.apache.http.client.entity.UrlEncodedFormEntity;
import org.apache.http.client.methods.CloseableHttpResponse;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClients;
import org.apache.http.message.BasicNameValuePair;
import org.apache.http.util.EntityUtils;

import java.io.IOException;
import java.util.ArrayList;
import java.util.List;

/**
 * @project: crawler_learning
 * @description: 学习使用有参HttpPost(String uri):爬取B站中搜索关键字为jsoup的网页内容(注:其并不支持POST,既只做演示)
 * @author: 黄宇辉
 * @date: 7/8/2019-1:12 PM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public class PostParamTest {

    public static void main(String[] args) throws IOException {

        //指定待爬取的网页链接
        HttpPost httpPost = new HttpPost("https://search.bilibili.com/all");
        //封装表单中的参数
        List<NameValuePair> params = new ArrayList<>();
        params.add(new BasicNameValuePair("keyword", "jsoup"));//设置请求参数
        UrlEncodedFormEntity formEntity = new UrlEncodedFormEntity(params, "utf-8");//创建表单中的Entity对象
        httpPost.setEntity(formEntity);//将表单的Entity对象到Post请求中
        httpPost.setHeader("User-Agent", "x-x-x-x-x-x");//设置Header信息
        //创建HttpClient对象
        CloseableHttpClient httpClient = HttpClients.createDefault();
        //发起请求,返回响应
        CloseableHttpResponse response = httpClient.execute(httpPost);
        //解析响应,获取数据
        if (response.getStatusLine().getStatusCode() == 200) {
            HttpEntity httpEntity = response.getEntity(); //获取响应体
            String content = EntityUtils.toString(httpEntity, "utf-8");//获取静态页面
            System.out.println(content);
        }
        //释放资源
        response.close();
        httpClient.close();

    }

}
```



### HttpClient连接池
*学习使用HttpClient连接池*
```java
package pers.huangyuhui.crawler.HttpClient_ConnPool;

import org.apache.http.HttpEntity;
import org.apache.http.client.methods.CloseableHttpResponse;
import org.apache.http.client.methods.HttpGet;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClients;
import org.apache.http.impl.conn.PoolingHttpClientConnectionManager;
import org.apache.http.util.EntityUtils;

/**
 * @project: crawler_learning
 * @description: 学习使用HttpClient连接池
 * @author: 黄宇辉
 * @date: 7/8/2019-3:45 PM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public class PoolTest {

    public static void main(String[] args) {
        //创建连接池管理器
        PoolingHttpClientConnectionManager phccm = new PoolingHttpClientConnectionManager();
        phccm.setMaxTotal(100);//最大连接数
        phccm.setDefaultMaxPerRoute(10);//每个主机的最大连接数

        doGet(phccm);
    }

    /**
     * @description: 爬取B站静态网页
     * @param: phccm
     * @date: 2019-07-08 4:15 PM
     * @return: void
     */
    private static void doGet(PoolingHttpClientConnectionManager phccm) {
        //指定待爬取网页的url
        HttpGet httpGet = new HttpGet("https://www.bilibili.com/");
        httpGet.setHeader("User-Agent", "x-x-x-x-x-x");
        //创建HttpClient对象:每次从连接池中获取HttpClient对象
        CloseableHttpClient httpClient = HttpClients.custom().setConnectionManager(phccm).build();
        //发起请求,获取响应
        try (CloseableHttpResponse response = httpClient.execute(httpGet)) {
            //解析响应,获取数据
            if (response.getStatusLine().getStatusCode() == 200) {
                HttpEntity httpEntity = response.getEntity(); //获取响应体
                String content = EntityUtils.toString(httpEntity, "utf-8");//获取静态页面
                System.out.println(content);
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
        //httpClient.close(); //由PoolingHttpClientConnectionManager管理
    }
}
```
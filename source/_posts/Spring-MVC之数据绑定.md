---
title: Spring MVC之数据绑定
date: 2019-05-22 21:25:32
tags: [Spring MVC]
---

## 学习笔记 : Spring MVC之数据绑定
*简介 : 在执行程序时,Spring MVC会根据客户端请求参数的不同,将请求消息中的信息以一定的方式转换并绑定到控制器类的方法参数中.这种将`请求消息`与`后台方法参数`建立连接的过程就是Sping MVC中的数据绑定.*

### 数据绑定介绍
*在数据绑定过程中,Spring MVC框架会通过数据绑定组件(`DataBinder`)将请求参数串的内容进行类型转换,然后将和转换后的值赋给控制器类中方法的形参,这样后台就可以正确绑定并获取客户端请求携带的参数了.整个数据绑定过程如下图所示 :*

![ ](Spring-MVC之数据绑定/SpringMVC-DataBinder.png)

*上图信息处理过程的步骤如下 :*
1. Spring MVC将`ServletsRequest`对象传递给`DataBinder`.
2. 将处理方法的入参对象传递给`DataBinder`.
3. `DataBinder`调用`ConversionService`组件进行数据类型转换,数据格式化等工作,并将`ServletRequest`对象中的消息填充到参数对象中.
4. 调用`Validator`组件对已经绑定了请求消息数据的参数对象进行数据合法性效验.
5. 效验完成后生成数据绑定结果`BindingResult`对象,Spring MVC会将`BindingResult`对象中的内容赋给处理方法的相应形参.
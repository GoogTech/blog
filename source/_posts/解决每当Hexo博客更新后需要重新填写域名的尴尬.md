---
title: 解决每当Hexo博客更新后需要重新填]写域名的尴尬
date: 2020-06-22 23:42:25
tags: [Hexo,GitHub,domain name]
---

1. *在 Hexo 博客文件夹中的 `source` 目录下( 不是hexo根目录哟 )创建一个名为 `CNAME`文件，然后文件中写入你的域名，例如我的博客域名 : `ishacker.net`*

![ ](解决每当Hexo博客更新后需要重新填写域名的尴尬/hexo-source-cname.PNG)


2. *执行 Hexo 博文生成命令 : `hexo g`，然后通过 `hexo d` 或 `git push origin` 等方式将 Hexo 博客部署到 Github 即可.*

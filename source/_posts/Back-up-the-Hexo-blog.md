---
title: Back up the Hexo blog
date: 2019-04-07 09:55:40
tags: [Hexo,github_]
---

## 文章主题 : 备份及恢复`Hexo`博客的详细教程

- *备份原理: `master branch`: 管理`Hexo`生成的静态网页.  `HexoBackup branch`: 管理`Hexo 重要的本地源文件及配置文件.`*

### 前言 : 如果你利用`Hexo`搭建了一个静态的网站用于写博客,会发现自己利用`Hexo deploy`的都是`Hexo generate`生成的静态网页.而下面的目录并没有被`push`到远程仓库 !
  
- *需要备份的目录及配置文件*
1. `source`目录 : 博客文章的`.md`文件.(需要备份)
2. `themes` 目录 : 主题文件夹.(需要备份)
3. `scaffolds`目录 : 文章的模板.(需要备份)
4. `package.json` : 安装包的名称.(需要备份)
5. `.gitignore` : 限定在`push`时哪些文件可以忽略.(需要备份)
6. `_config.yml` : 站点的配置文件.(需要备份)

- *不需要备份的目录及配置文件*
1. `node_modules/` : 是安装包的目录，在执行`npm install`的时候会重新生成.(不需要备份)
2. `.git/` : 主题和站点都有，标志这是一个git项目.(不需要备份)
3. `public`是`hexo generate`生成的静态网页.(不需要备份)
4. `.deploy_git` : 同`3`,`hexo g`也会生成.(不需要备份)
5. `db.json` : 配置文件.(不需要备份)

- *注 : 不需要拷贝的文件正是`.gitignore`中所忽略的哟 !*

### 备份
1. 创建`GitHub`仓库`Username.github.io`如果同名仓库之前已经创建,请将之前的仓库改名,新建的仓库必须是`Username.github.io`.
2. 在`Username.github.io`仓库中创建一个新的分支`HexoBackup`.(此时的默认分支是: `master`)
3. 在该仓库中的`Setting`中将`HexoBackup`修改为为默认分支.
4. 将刚刚创建的新仓库`clone`至本地后将之前你存放`Hexo bolg`文件夹中的`_config.yml`,`themes/`,`source/`,`scaffolds/`,`package.json`,`.gitignore`复制至你所`clone`下来的`Username.github.io`文件夹中.
5. 将`themes/theme-name/`中的`.git/`删除,否则无法将主题文件夹`push`到远程分支.(`易错点 : 详情见文章最后的注意事项`)
6. 在`Username.github.io`文件夹执行`npm install`和`npm install hexo-deployer-git`(注意: 此时在该文件内的分支名应该显示为:`HexoBackup`).
7. 通过执行`git add .`,`git commit -m "A description of the information submitted"`,`git push origin HexoBackup`来提交`Hexo bolg`网站的源文件.
8. 如果更新博客文章后可以通过执行`hexo g -d`生成静态网页部署到`Github`上.


### 更新( 在本地对博客修改: 包括修改主题样式,发布新文章等.. )
- *以下两个步骤没有严格的顺序,小伙伴们随心所欲呗 ~*
1. 首先执行`hexo g -d`生成静态网页部署至`Github`上.
2. 其次依次执行`git add .`,`git commit -m ""`,`git push origin HexoBackup`来提交`Hexo bolg`网站源文件.


### 恢复 ( 重装电脑后,或者在其它电脑上想修改自己的博客.. )
1. 安装`git`.
2. 安装`Nodejs`和`npm`.
3. 将博客仓库`clone`到本地.
5. 在文件夹内执行命令`npm install hexo-cli -g`,`npm install`,`npm install hexo-deployer-git`.


### 注意事项 ( `Hexo`无法备份`themes`主题目录中的部分`theme`.. )
- *如果想正常备份你所喜欢的`theme`的话,可以在博客目录运行如下命令 :*
```java
$ git rm --cached themes\theme-name\
rm 'themes/theme-name'

$ git status
On branch master
Your branch is up-to-date with 'origin/HexoBackup'.

Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

        deleted:    themes/theme-name

Untracked files:
  (use "git add <file>..." to include in what will be committed)

        themes/theme-name/

//最后再执行: `git add themes/theme-name/` 就会发现可以成功备份该主题了哟 !
```
- **原因分析 : 这是因为用到了`git`的子模块(`git submodule`)功能,既你在你的`git`项目里`clone`的他人的项目.在你的主项目的`git`库里,子模块只是一个`HEAD`指针,指向子模块的`commit` ..详情见下述参考的博客文章哟 (っ•̀ω•́)っ✎⁾⁾**


- *参考博客文章如下( 甚是感谢 )* :
- [https://blog.itswincer.com/posts/7efd2818/](https://blog.itswincer.com/posts/7efd2818/)
- [https://hoxis.github.io/hexo-backup-theme-dir.html](https://hoxis.github.io/hexo-backup-theme-dir.html)







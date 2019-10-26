---
title: Git Commit Message 规范
date: 2019-08-08 19:59:13
tags: [github_]
---

## 学习笔记 : Git Commit Message规范
*Git Commit Message 应该清晰明了,要用精简的语言说明本次提交的目的,其主要作用是为了后续的搜索、版本的回滚、合并冲突的追溯等操作~*

### 规范介绍
*这次主要介绍 AngularJS 的规范,它是由 Google 推出的一套提交消息规范标准,也是目前使用范围最广的规范,比较合理和系统化,并且还有配套的工具可以供我们使用,规范执行方案如下 :*

![](Git-Commit-Message-规范/git-commit-message-mindmap.png)


### 格式要求
```html
<type>(<scope>): <subject>
<BLANK LINE>
<body>
<BLANK LINE>
<footer>
```
- 消息只占用一行,任何行都不能超过 100 个字符
- 允许使用 GitHub 以及各种 Git 工具阅读消息
- 提交消息由页眉、正文和页脚组成,由空行分隔


#### Header
*Header部分只有一行,包括三个字段 : `type`(必需)、`scope`(可选)、和`subject`(必需)*

1. *`type`代表某次提交的类型,比如是修复一个 bug 或是增加一个 feature,类型如下 :*

|   类型   |                          说明                          |
| :------: | :----------------------------------------------------: |
|   feat   |                      新增feature                       |
|   fix    |                        修复bug                         |
|   docs   | 仅仅修改了文档,比如README , CHANGELOG , CONTRIBUTE等等 |
|  style   |       修改了空格,格式缩进,逗号等,不改变代码逻辑        |
| refactor |            代码重构,没有添加新功能或修复Bug            |
|   perf   |               优化相关,比如提升性能,体验               |
|   test   |            测试用例,包括单元测试,集成测试等            |
|  chore   |           改变构建流程,或者增加依赖库,工具等           |
|  revert  |                    回滚到上一个版本                    |

2. *`scope`用于说明 commit 影响的范围,可以是指定提交更改位置的任何内容,如 :*
- 对 package.json 文件新增依赖库，chore(package.json): 新增依赖库
- 或对代码进行重构，refacto(weChat.vue): 重构微信进件

3. *`subject`是 commit 目的的简短描述,不超过50个字符. 如果没有更合适的范围,可以直接写提交内容,格式要求如下 :*
* 以动词开头,使用第一人称现在时,比如change,而不是changed或changes
* 第一个字母小写
* 结尾不加句号( . )


#### Body
*Body 部分是对本次 commit 的详细描述,可以分成多行. 下面是一个范例 :*
```
More detailed explanatory text, if necessary.  Wrap it to 
about 72 characters or so. 

Further paragraphs come after blank lines.

- Bullet points are okay, too
- Use a hanging indent
```
*有两个注意点 :*
* 使用第一人称现在时,比如使用change而不是changed或changes
* 应该说明代码变动的动机,以及与以前行为的对比


#### Footer
*Footer 部分只用于两种情况 :*
1. *不兼容变动 : 如果当前代码与上一个版本不兼容,则 Footer 部分以`BREAKING CHANGE`开头,后面是对变动的描述、以及变动理由和迁移方法,示例如下 :*
```
BREAKING CHANGE: isolate scope bindings definition has changed.

    To migrate the code follow the example below:

    Before:

    scope: {
      myAttr: 'attribute',
    }

    After:

    scope: {
      myAttr: '@',
    }

    The removed `inject` wasn't generaly useful for directives so there should be no code using it.
```

2. *关闭 Issue : 如果当前 commit 针对某个issue,那么可以在 Footer 部分关闭这个 issue :*
```
Closes #234
```
*也可以一次关闭多个 issue :*
```
Closes #123, #245, #992
```


#### Revert
*还有一种特殊情况,如果当前 commit 用于撤销以前的 commit,则必须以`revert:`开头，后面跟着被撤销 Commit 的 Header,示例如下 :*
```
revert: feat(pencil): add 'graphiteWidth' option

This reverts commit 667ecc1654a317a13331b17617d973392f415f02.
```
*Body部分的格式是固定的，必须写成`This reverts commit &lt;hash>`,其中的`hash`是被撤销 commit 的`SHA`标识符。*



*✨规范参考链接 :*
* https://github.com/vuejs/vue/commits/dev
* https://github.com/angular/angular/commits/master

*🙏文章参考链接(获益匪浅,非常感谢) :*
* https://www.cnblogs.com/wubaiqing/p/10307605.html
* http://www.ruanyifeng.com/blog/2016/01/commit_message_change_log.html
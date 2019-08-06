---
title: 详解Intellij IDEA中的DEBUG功能
date: 2019-08-06 15:21:34
tags: [IDEA]
---

## 学习笔记 : 详解Intellij IDEA中的DEBUG功能
*`Debug`用于追踪代码的执行流程,其主要用途如下 :*
1. *若程序运行过程中出现异常,可通过启用Debug模式来分析程序运行过程中各个参数的变化,继而定位到异常发生的位置*
2. *通过启用Debug模式来跟踪代码的运行流程,继而去学习一些第三方框架的源码*


### 基本用法&快捷键
*IDEA版本 : Intellij IDEA 2019.1.3-UItimate Edition*

![](详解Intellij-IDEA中的DEBUG功能/IDEA-debug-view-1.PNG)

*如上debug视图,从上到下依次讲解 :*
1. *`Show Execution Point (Alt + F10)` : 如果你的光标在其它行或其它页面,点击这个按钮可跳转到当前代码执行的行*
2. *`Step Over (F6)` : 步过,一行一行地往下走,如果这一行上有方法,并不会进入*
3. *`Step Into (F5)` : 步入,如果当前行有方法,可以进入方法内部,一般用于进入自定义方法内,不会进入官方类库的方法*
4. *`Force Step Into (Alt + Shift + F7)` : 强制步入,能进入任何方法,查看底层源码的时候可以用这个进入官方类库的方法*
5. *`Step Out (F7)` : 步出,从步入的方法内退出到方法调用处,此时方法已执行完毕,只是还没有完成赋值*
6. *`Drop Frame (默认无)` : 回退断点,后面章节详细说明*
7. *`Run to Cursor (Ctrl + R)` : 运行到光标处,你可以将光标定位到你需要查看的那一行,然后使用这个功能,代码会运行至光标行,而不需要打断点哟*
8. *`Evaluate Expression (Ctrl + U)` : 计算表达式,后面章节详细说明*


*从左到右依次讲解 :*

![](详解Intellij-IDEA中的DEBUG功能/IDEA-debug-view-2.PNG)

*如上debug视图,从左到右依次讲解 :*
1. *`Rerun 'xxx'(Ctrl + F11)` : 重新运行程序,会关闭服务后重新启动程序*
2. *`Update 'xxx' application (Ctrl + F10)` : 更新程序,一般在你的代码有改动后可执行这个功能. 而这个功能对应的操作则是在服务配置里,一般配合热部署插件会更好用,如JRebel,这样就不用每次更改代码后还要去重新启动服务啦*
3. *`Resume Program (F8)` : 恢复程序,比如你在第20行和25行有两个断点,当前运行至第20行时按F9,则运行到下一个断点(即第25行),再按F9则运行完整个流程,因为后面已经没有断点咯*
4. *`Pause Program` : 暂停程序,启用Debug*
5. *`Stop 'xxx' (Ctrl + F2)` : 连续按两下会关闭程序,有时候你会发现关闭服务再启动时,报端口被占用,这是因为没完全关闭服务的原因,这时就需要你查杀指定的JVM进程啦*
6. *`View Breakpoints (Ctrl + Shift + F8)` : 查看所有断点,后面章节会涉及到*
7. *`Mute Breakpoints` : 哑的断点,既选择这个后,所有断点变为灰色,断点失效,按F9则可以直接运行完程序. 再次点击,断点变为红色,有效. 如果只想使某一个断点失效,则可以在断点上右键,然后取消Enabled*



*更多内容请参考(老版本哟,无碍啦~) : https://www.cnblogs.com/nihaorz/p/7613967.html .*
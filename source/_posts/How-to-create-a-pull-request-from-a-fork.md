---
title: How to create a pull request from a fork
date: 2019-07-29 18:03:07
tags: [github_]
---

## 学习笔记 : 如何在`fork`的仓库中创建一个`pull request`请求
*如果你创建了仓库的分叉并对分叉进行了更改,你可以创建一个拉取请求来请求上游仓库接受你的更改. 至于说如何正确使用分支请参阅官方文档 : https://help.github.com/en/articles/working-with-forks*

### 详细步骤
1. *导航到从其中创建分叉的原始仓库*


2. *在`Branch`(分支)菜单的右侧,单击`New pull request`(新建拉取请求)*

![](How-to-create-a-pull-request-from-a-fork\pull-request-start-review-button.png)


3. *在`Compare `(比较)页面上,单击`compare across forks`(跨分叉比较)*

![](How-to-create-a-pull-request-from-a-fork\compare-across-forks-link.png)


4. *确认基本分叉是您要向其合并更改的仓库. 使用`base branch`(基本分支)下拉菜单,选择要向其合并更改的上游仓库分支*

![](How-to-create-a-pull-request-from-a-fork\choose-base-fork-and-branch.png)


5. *使用`head fork`(头部分叉)下拉菜单选择您的分叉,然后使用`compare branch`(比较分支)下拉菜单选择进行了更改的分支*

![](How-to-create-a-pull-request-from-a-fork\choose-head-fork-compare-branch.png)


6. *键入拉取请求的标题和说明*
 
![](How-to-create-a-pull-request-from-a-fork\pullrequest-description.png)


7. *如果您不希望对上游仓库具有推送权限的任何人更改您的拉取请求,请取消选中`Allow edits from maintainers`(允许维护员编辑)*
   
![](How-to-create-a-pull-request-from-a-fork\allow-maintainers-to-make-edits.png)


8. *要创建可供审查的拉取请求,请单击 Create Pull Request(创建拉取请求). 要创建拉取请求草稿,请使用下拉菜单选择`Create Draft Pull Request`(创建拉取请求草稿),然后单击`Draft Pull Request`(拉取请求草稿)*

![](How-to-create-a-pull-request-from-a-fork\pullrequest-send.png)



- *👍 摘自GiHub帮助文档(推荐将其作为GitHub学习参考,内容真心超级详细哟 ~) : https://help.github.com/en*
---
title: A simple comment component for Vue
date: 2019-07-25 11:11:04
tags: [Vue]
---

## 案例笔记 : 一个简单的Vue评论框组件
*在写完案例 : [一个基于Vue.js的简单信息管理页](https://github.com/YUbuntu0109/YUbuntu0109.github.io/blob/83b7f5359d1092086a2d13fc6cf2b6c6414d415b/source/_posts/%E4%B8%80%E4%B8%AA%E5%9F%BA%E4%BA%8EVue%E7%9A%84%E7%AE%80%E5%8D%95%E4%BF%A1%E6%81%AF%E7%AE%A1%E7%90%86%E9%A1%B5.md)后,小哥又初步学习了Vue.js组件的定义及基本用法,来为此再写一个小总结案例吧~ 这是一个简单的评论框组件 : 发表评论(将其存储到`Local Storage`)~ 该程序其它版本见 : https://github.com/YUbuntu0109/vue-learning*


*(simple component for comment v1.0/comment-component-1.0.html)程序如下所示 :*
```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<script src="../../resources/vue.js"></script>
		<link rel="stylesheet" href="../../resources/bootstrap.css" />
		<title>the comment component v.10</title>
	</head>
	<body>
		<div id="app">
			<!-- use the comment component -->
			<comm-component @fun-init="initComments" @fun-clear="clearLocalStorage"></comm-component>
			<!-- comments -->
			<ul class="list-group">
				<li class="list-group-item" v-for="comment in comments" :key="comment.id">
					<span class="badge">commenter : {{comment.commenter}}</span>
					contents : {{comment.content}}
				</li>
			</ul>
		</div>
		<!-- the template of the component -->
		<template id="commTemplate">
			<div class="panel panel-primary">
				<div class="panel-heading">
					<h3 class="panel-title">A SIMPLE COMMENT COMPONENT FOR VUE</h3>
				</div>
				<div class="panel-body form-inline">
					<div class="form-group">
						<label class="label label-default" style="font-size: 17px;">commenter</label>
						<input type="text" class="form-control" style="width: 180px;" v-model="commenter" v-focus v-color="'#777'" />
					</div>
					<div class="form-group" style="padding-left: 15px;">
						<label class="label label-default" style="font-size: 17px;">content</label>
						<input type="text" class="form-control" style="width: 500px;" v-model="content" v-color="'#777'" @keyup.enter="commit" />
					</div>
					<label style="padding-left: 5px;">
						<button class="btn btn-success" @click="commit">COMMIT</button>
						<button class="btn btn-warning" style="margin-left: 10px;" @click="reset">RESET</button>
						<button class="btn btn-danger" style="margin-left: 10px;" @click="delAll">CLEAR STORAGE</button>
					</label>
				</div>
			</div>
		</template>

		<script>
			//declare the comment component
			var commComponent = {
				template: '#commTemplate',
				data() {
					return {
						commenter: '',
						content: ''
					}
				},
				methods: {
					commit() {
						var comment = {
							id: Date.now(),
							commenter: this.commenter,
							content: this.content
						}
						var commentList = JSON.parse(localStorage.getItem('cmts') || '[]') //get all of comments from the local storage
						commentList.unshift(comment)
						localStorage.setItem('cmts', JSON.stringify(commentList)) //save the new comment list
						this.$emit('fun-init')
						this.commenter = ''
						this.content = ''
					},
					reset() {
						this.commenter = ''
						this.content = ''
					},
					delAll() {
						this.$emit('fun-clear')
						this.$emit('fun-init')
					}
				}
			}

			//custom directive
			Vue.directive('focus', {
				inserted: function(el) {
					el.focus()
				}
			});
			Vue.directive('color', {
				bind: function(el, colorStr) {
					el.style.color = colorStr.value
				}
			});

			var vm = new Vue({
				el: '#app',
				data: {
					idStr: [],
					comments: [{
						id: '',
						commenter: '',
						content: ''
					}]
				},
				methods: {
					//initializes the list of comments on the page
					initComments() {
						var commentList = JSON.parse(localStorage.getItem('cmts') || '[]')
						this.comments = commentList
					},
					//delete all of comments from the local storage
					clearLocalStorage() {
						localStorage.clear()
					}
				},
				components: {
					commComponent
				},
				created() {
					this.initComments() //(reflush)reload comments
				}
			});
		</script>
	</body>
</html>
```

*程序运行效果如下图所示 :*
![](A-simple-comment-component-for-Vue\a-simple-comment-component-for-vue-v1.0.PNG)
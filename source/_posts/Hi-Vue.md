---
title: Hi Vue ~
date: 2019-07-19 19:35:40
tags: [Vue]
---

## 学习笔记 : 拥抱Vue ~
*传统的网站开发一般采用`HTML`,`CSS`,`JS`作为技术架构,而`Vue`立足于其上,并以模板语法为基础,数据绑定和组件化开发为核心,极大地简化了项目的开发流程 ~*


### Vue简介
*`Vue.js`是一套轻量级`MVVM`框架,由时任前Google工程师(现任阿里Week团队技术顾问)的尤雨溪创造并开源,与其它重量级的框架不同的是,`Vue`的核心库只关注视图层,并且提供尽可能简单的API以实现数据绑定,组件复用等机制,且非常容易学习并混入其它库. 其Github开源地址 : https://github.com/vuejs/vue*


### MVVM介绍
*模型-视图-控制器(`Model-View-Controller`,MVC)模式,在Web 1.0时代曾被广泛应用于Web架构中,然而其诞生时间比Web早几年,最初,MVC被应用于桌面程序中,然而在PHP,JSP等脚本语言诞生后,也逐渐成为Web开发的主流模式 ! 其`MVVM`作为`MVC`的其中一个衍生版本,MVVM主要的目的是分离视图(View)和模型(Model),`ViewModel`层封装了界面展示和操作的属性的接口. 通过数据绑定,我们可以将View和ViewModel关联一起,当ViewModel中的数据发生变化时,View也会同步进行更新,View与ViewModel之间通过双向绑定(`data-binding`)建立联系,如下图所示 :*

![](Hi-Vue/MVVM-模式.jpg)
- *View : 视图层主要负责展示视图*
- *ViewModel : 视图-模型层起到桥梁的作用,一方面响应用户事件并向模型层发送请求,另一方面将模型层返回的数据通过数据数据绑定在视图层中展示*
- *Model : 模型层主要负责处理交互请求并返回响应的数据*


*`MVVM`模式解耦了视图和模型 : 在模式中,每一个视图都对应一个ViewModel,同时ViewModel与模型建立关联系,当接收到用户请求时,ViewModel获取模型响应数据,并通过数据绑定将相应的视图页面重新渲染. 视图层的数据只需要传入ViewModel即可实现视图层的同步更新,从而实现了视图和模型之间的松散耦合 .*


*下面以我的第一个`Vue`实例为例(具体语法解析见下述哟),简单地解析一下`MVVM`的各个部分 :*
```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<script src="resources/vue.js"></script>
		<title>first program for Vue</title>
	</head>
	<body>
		<div id="app">{{ message }}</div>
		<script>
			var app = new Vue({
				el: '#app',
				data: {
					message: 'Hello Vue!'
				}
			});
		</script>
	</body>
</html>
```

1. *View 层*
```html
<div id="app">{{ message }}</div>
```

2. *ViewModel 层*
```html
<script>
var app = new Vue({
	el: '#app',
	data: {
		message: 'Hello Vue!'
	}
});
</script>
```

3. *Model 层*
```html
<script>
data: {
	message: 'Hello Vue!'
}
</script>
```


### 第一个Vue实例
*在这个实例中,初始化了带有'message'数据的'vm'对象,并将其绑定到'id'为'app'的`DOM`节点上. 初始化时,在实例上绑定的常规数据对象会被Vue转换为被观察的拥有可响应行为的对象,简单点说,就是当数据发生改变时,会同步更新其数据链和作用域中所有的相关状态,例如当实例数据发生改变时,视图也随之变化. 下面就义这个简单的Vue实例为例,简单地解析一下其语法 :*
```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<script src="resources/vue.js"></script>
		<title>first program for Vue</title>
	</head>
	<body>
		<div id="app">{{ message }}</div>
		<script>
			var app = new Vue({
				el: '#app',
				data: {
					message: 'Hello Vue!'
				}
			});
		</script>
	</body>
</html>
```

1. *vm : 通过构造函数Vue就可以创建一个Vue的根实例,并启动Vue应用,变量vm就代表这个Vue实例. 例如 :*
```html
<script>
var vm = new Vue({
	
});
</script>
```

2. *el : 实际上,所有代码都是一个对象写入Vue实例(`vm`)内的. 首先,必不可少的一个选项就是`el`,其用于指定一个页面中已存在的`DOM`元素来挂载Vue实例,它可以是HTMLElement,也可以是CSS选择器,挂载成功后继而可以通过`vm.$el`来访问该元素,例如 :*
```html
<div id="app"></div>

<script>
var vm = new Vue({
    el:document.getElementById('app') //或:'#app'
});
<script/>
```

3. *data : 通过Vue实例的data选项,继而配合`v-model`指令可以声明应用内需要双向绑定的数据,例如 :*
```html
<input type="text" v-model="msg" />

<script>
var vm = new Vue({
	el: '#app',
	data: {
		msg: 'stay hungray,stay foolish ~'
	}
});
</script>
```

4. *双括号(`Mustache`语法)是最基本的文本插值方法,它会自动将我们双向绑定的数据实时显示出来,例如 :*
```html
<div id="app">{{ message }}</div>

<script>
    var vm = new Vue({
	el: '#app',
	data: {
		message: 'Hello Vue!'
	}
});
</script>
```


*📖 参考,推荐书籍 :*
- *《Vue.js实战》—— 梁灏 · 编著*
- *《Vue.js从入门到项目实战》—— 刘汉伟 · 编著*
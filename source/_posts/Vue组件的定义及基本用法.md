---
title: Vue组件的定义及基本用法
date: 2019-07-24 19:57:46
tags: [Vue]
---

## 学习笔记 : Vue组件的定义及基本的使用方法
*组件的出现,是为了拆分实例的代码量的,继而能够让我们以不同的组件来划分不同功能模块. 组件是可复用的Vue实例,所以它们与`new Vue`接收相同的选项,例如 data、computed、watch、methods以及生命周期钩子等 ..*

* 组件化 : 从代码逻辑的角度进行划分,保证每个功能模块的职能单一
* 模块化 : 从UI设计的角度进行划分,保证前端方便UI组件的重用


### 组件的注册
#### 全局组件注册方式
*方式一 : 使用`Vue.extend`,及其简化模式*
```html
<div id="app">
	<my-component></my-component>
	<simple-my-component1></simple-my-component1>
	<simple-my-component2></simple-my-component2>
</div>
<script>
    //注册组件
	var component = Vue.extend({
		template: '<h2>Stay hungray,Stay foolish</h2>'
	});
	Vue.component('myComponent', component)

	//简化模式1
	Vue.component('simpleMyComponent1', Vue.extend({
				template: '<h3>Stay hungray,Stay foolish</h3>'
	}));

    //简化模式2
    Vue.component('simpleMyComponent2', {
				template: '<div><h4>Stay hungray,Stay foolish</h4><span>data</span></div>'
	});

	var vm = new Vue({
		el: '#app',
		data: {}
	});
</script>
```

*方式二 : 将`template`定义为独立标签,提高代码可读性*
```html
<template id="myTemplate">
	<h2>Stay hungray,Stay foolish</h2>
</template>
<div id="app">
	<simple-my-component></simple-my-component>
</div>
<script>
     //注册组件
	Vue.component('simpleMyComponent', Vue.extend({
		template: '#myTemplate'
	}));

	var vm = new Vue({
		el: '#app',
		data: {}
	});
</script>
```

#### 局部组件注册方式
*方式一 : `components`选项用于为组件注册从外部引入的组件*
```html
<template id="myTemplate">
	<h2>this is my component</h2>
</template>
<div id="app">
	<my-component></my-component>
</div>
<script>
	var myComponent = {
		template: '#myTemplate'
	}
	var vm = new Vue({
		el: '#app',
		components: {
			myComponent
		}
	});
</script>
```

*方式二*
```html
<div id="app">
	<my-title1></my-title1>
	<my-title2></my-title2>
</div>
<script>
	let myTitle1 = {
		name: 'myTitle',
		template: '<h2>Stdy hungry,Stdy foolish</h2>'
	}
	let myTitle2 = {
		name: 'myTitle2',
		template: '<h3>Stdy hungry,Stdy foolish</h3>'
	}
	var vm = new Vue({
		el: '#app',
		components: {
			myTitle1,
			myTitle2
		}
    });
</script>
```


### Prop
*父组件在引用子组件的同时,可以通过属性绑定(v-bind)的形式把需要传递给子组件的数据,以`属性绑定`的形式,传递到子组件内部,供子组件使用,示例程序如下 :*
```html
<div id="app">
	<son-component v-bind:parentmsg="msg"></son-component>
</div>
<script>
	var vm = new Vue({
		el: '#app',
		data: {
			msg: '这是父组件中的数据'
		},
		components: {
			sonComponent: {
				props: ['parentmsg'], //只读
				template: '<h3>这是子组件--->{{parentmsg}}</h3>',
			}
		}
	});
</script>
```


### 自定义事件
*父组件向子组件传递方法,使用的是事件绑定机制,示例程序如下 :*
```html
<div id="app">
	<my-component @func="show"></my-component>
</div>
<script>
	var myComponent = {
		template: '#myTemplate',
		methods: {
			getMethods() {
				this.$emit('func', '你好,我是子组件!')
			}
		}
	}
	var vm = new Vue({
		el: '#app',
		methods: {
			show(data) {
				window.alert('这是父组件中的方法,子组件传递过来的数据为:' + data)
			}
		},
		components: {
			myComponent
		}
	});
</script>
```


### 动态组件
*组件间的切换,方式一 : 使用`if-else`,示例程序如下 :*
```html
<div id="app">
	<button @click="flag=true">登录</button>
	<button @click="flag=false">注册</button>
	<login v-if="flag"></login>
	<regist v-else="flag"></regist>
</div>
<script>
	Vue.component('login', {
		template: '<h3>这是登录组件</h3>'
	});
	Vue.component('regist', {
		template: '<h3>这是注册组件</h3>'
	});
	var vm = new Vue({
		el: '#app',
		data: {
			flag: true
		}
	});
</script>
```

*组件间的切换,方式二 : 使用`component`元素,示例程序如下 :*
```html
<div id="app">
	<button @click="componentName='login'">登录</button>
	<button @click="componentName='regist'">注册</button>
	<button @click="componentName='logout'">注销</button>
	<component :is="componentName"></component>
</div>
<script>
	Vue.component('login', {
		template: '<h3>这是登录组件</h3>'
	});
	Vue.component('regist', {
		template: '<h3>这是注册组件</h3>'
	});
	Vue.component('logout', {
		template: '<h3>这是注销组件</h3>'
	});
	var vm = new Vue({
		el: '#app',
		data: {
			componentName: 'login' //default
		}
	});
</script>
```
---
title: Vue.js的自定义指令与过滤器
date: 2019-07-23 15:45:39
tags: [Vue]
---

## 学习笔记 : Vue的自定义指令及过滤器的使用

### 指令
*先前我们接触过Vue提供的'开箱即用的'指令,如`v-bind`,`v-on`,`v-model`等 . .除了这些指令外,Vue也允许我们使用一些自定义的指令哟~ 一个指令定义对象可以提供如下几个钩子函数 (均为可选) :*

1. *`bind` : 只调用一次,指令第一次绑定到元素时调用. 在这里可以进行一次性的初始化设置*
2. *`inserted` : 被绑定元素插入父节点时调用(仅保证父节点存在,但不一定已被插入文档中)*
3. *`update` : 所在组件的VNode更新时调用,但是可能发生在其子VNode更新之前. 指令的值可能发生了改变,也可能没有. 但是你可以通过比较更新前后的值来忽略不必要的模板更新 (详细的钩子函数参数见 : https://cn.vuejs.org/v2/guide/custom-directive.html)*
4. *`componentUpdated` : 指令所在组件的VNode及其子VNode全部更新后调用*
5. *`unbind` : 只调用一次,指令与元素解绑时调用*


#### 自定义全局指令
*自定义一个全局的指令,用于自定义字体颜色,示例程序如下 :*
```html
<div id="app">
	<p v-color="'blueviolet'">{{msg}}</p>
</div>
<script>
	Vue.directive('color', {
		bind: function(el, colorStr) {
			el.style.color = colorStr.value
		}
	});

	var vm = new Vue({
	    el: '#app',
		data: {
			msg: 'hi Vue !'
		}
	});
</script>
```

*大多情况下,我们可能只想在`bind`和`update`钩子上作重复动作,并不想关心其它的钩子,那么就可以这样简写哟 :*
```html
<div id="app">
	<p v-color="'blueviolet'">{{msg}}</p>
</div>
<script>
	Vue.directive('color', function(el, colorStr) {
		el.style.color = colorStr.value
	});

	var vm = new Vue({
	    el: '#app',
		data: {
			msg: 'hi Vue !'
		}
	});
</script>
```

#### 自定义私有指令 
*自定义一个私有的指令,用于自定义字体颜色,其示例程序如下 :*
```html
<div id="app">
	<p v-color="'blueviolet'">{{msg}}</p>
</div>
<script>
	var vm = new Vue({
		el: '#app',
		data: {
			msg: 'hi Vue !'
		},
		directives: {
			'color': {
			    bind: function(el, colorStr) {
					el.style.color = colorStr.value
				}
			}
		}
	});
</script>
```

*(同上)可简写为 :*
```html
<div id="app">
	<p v-color="'blueviolet'">{{msg}}</p>
</div>
<script>
	var vm = new Vue({
		el: '#app',
		data: {
			msg: 'hi Vue !'
		},
		directives: {
		    'color': function(el, colorStr) {
			    el.style.color = colorStr.value
			}
		}
	});
</script>
```


### 过滤器
*Vue.js允许你自定义过滤器,可被用于一些常见的文本格式化,过滤器可以用在两个地方 : `双花括号插值`和`v-bind`表达式 (后者从 2.1.0+ 开始支持). 过滤器应该被添加在JavaScript表达式的尾部,由管道符 : `|` 指示,表达式的值将作为形参传入到`filter`中,示例代码如下 :*
```html
<div id="app">
	<h2>{{title}}</h2>
	<h2>{{title | titleFilter1}}</h2>
	<h2>{{title | titleFilter1 | titleFilter2}}</h2>
</div>
<script>
	var vm = new Vue({
		el: '#app',
		data: {
			title: 'Demo#%Program#%For#%Filter'
		},
		filters: {
			titleFilter1(value) {
				return value.replace(/#/g, ' ')
			},
			titleFilter2(value) {
				return value.replace(/%/g, '')
			}
		}
	});
</script>
```

*除在组件中定义filter外,Vue还允许在开发者在全局定义filter,与选项filter不同的是,全局filter可以在任何组件和实例中起作用哟,示例程序如下 :*
```html
<div id="app">
	<h2>{{title}}</h2>
	<h2>{{title | titleFilter1}}</h2>
	<h2>{{title | titleFilter1 | titleFilter2}}</h2>
</div>
<script>
	Vue.filter('titleFilter1', value => {
		return value.replace(/#/g, ' ')
	});
	Vue.filter('titleFilter2', value => {
		return value.replace(/%/g, '')
	});

	var vm = new Vue({
		el: '#app',
		data: {
			title: 'Demo#%Program#%For#%Filter'
		}
	});
</script>
```

*程序运行效果如下 :*
![](Vue-js的自定义指令与过滤器/Vue-filter-demo.PNG)


#### 全局过滤器
*定义一个全局filter,用于格式化指定字符,其示例程序如下 :*
```html
<div id="app">{{msg | msgFilter('Keep')}}</div>
<script>
	Vue.filter('msgFilter', function(msg, filterStr) {
		return msg.replace(/Stay/g, filterStr) //使用正则表达式匹配字符
	});

	var vm = new Vue({
		el: '#app',
		data: {
			msg: 'Stay hungry,Stay foolish ~'
		}
	});
</script>
```

#### 私有过滤器
*定义一个选项filter,用于格式化指定字符,其示例程序如下 :*
```html
<div id="app">{{msg | msgFilter('keep')}}</div>
<script>
    var vm = new Vue({
	el: '#app',
		data: {
			msg: 'Stay hungry,Stay foolish ~'
		},
		filters: { 
			msgFilter: function(msg, filterStr) {
				return msg.replace(/Stay/g, filterStr)
		    }
	    }
	});
</script>
```
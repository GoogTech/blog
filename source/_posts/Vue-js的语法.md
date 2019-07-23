---
title: Vue.js的语法
date: 2019-07-21 11:14:08
tags: [Vue]
---

## 学习笔记 : Vue.js的语法

### 数值绑定
#### 文本插值
*文本插值的方式十分简单,只需使用双括号(`Mustache`语法)将要绑定的变量,值,表达式括住即可,Vue将会获取计算后的值,并以文本的形式将其展示出来 .*
```html
<div id="app">{{ message }}</div>
<script>
	var app = new Vue({
		el: '#app',
		data: {
		    message: 'Hello Vue!'
		}
	});
</script>
```

#### HTML插值
*HTML插值是对文本插值的补充和扩展,其可以动态渲染DOM节点,常用于处理开发者无可预知和难以控制的DOM结构,如渲染用于随意书写的文档结构等 ..*
```html
<div id="app"><p v-html="message"></p></div>
<script>
	var v = new Vue({
	    el: '#app',
		data: {
		    message: '<h1 style="color: red;">v-html</h1>'
		}
	});
</script>
```



### 属性绑定
#### 指令-vind
*DOM节点的属性基本都可以用指令`v-bind`进行绑定,其简写为  '`:`',可绑定的属性可以是变量,表达式,执行函数等内容,不过最终的结果都应该满足属性自身的约束 .*
```html
<div id="app">
	<input type="button" value="按钮" v-bind:title="message+'可以拼接字符串哟 ~'" />
</div>
<script>
	var v = new Vue({
		el: '#app',
		data: {
			message: 'v-bind'
		}
	});
</script>
```

#### 类名和样式绑定
*由于类名`class`,和样式`style`在节点属性中是两个比较奇怪对的存在,`虽然他们可接收的类型都是字符串,但类名实际上是由数组拼接而成,而样式则是由对象属性键值对拼接而成的`,所以Vue在绑定类名和样式时也采用不一样的机制哟 !*

1. *通过字符串,数组,对象方式为节点动态绑定类名属性*
```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<script src="../resources/vue.js"></script>
		<title>Vue为节点绑定类型属性</title>
		<style type="text/css">
			.red {
				color: red;
			}

			.thin {
				font-weight: 50px;
			}

			.italic {
				font-style: italic;
			}

			.active {
				letter-spacing: 0.5em;
			}
		</style>
	</head>
	<body>
		<div id="app">
			<h2 :class="['red','italic']">使用数组</h2>
			<h2 :class="['red','italic',flag?'active':'']">数组中使用三元表达式</h2>
			<h2 :class="['red','italic',{'active':flag}]">数组中使用对象代替三元表达式</h2>
			<h2 :class="{red:true,thin:true,italic:false,active:true}">使用对象</h2>
			<h2 :class="classObj">使用对象</h2>
		</div>
		<script>
			var vm = new Vue({
				el: '#app',
				data: {
					flag: true,
					classObj: {
						red: true,
						thin: true,
						italic: false,
						active: true
					}

				}
			});
		</script>
	</body>
</html>
```

2. *Vue绑定样式的方式和类名相似,不过样式是以键值对的形式,所以不能像类名一样使用数组进行绑定哟 ~*
```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<script src="../resources/vue.js"></script>
		<title>Vue样式绑定</title>
	</head>
	<body>
		<div id="app">
			<h2 :style="{color:'red'}">Vue样式绑定</h2>
			<h2 :style="styleObj">Vue样式绑定</h2>
			<h2 :style="[styleObj,styleObj2]">Vue样式绑定</h2>
		</div>
		<script>
			var vm = new Vue({
				el: "#app",
				data: {
					styleObj: {
						color: 'forestgreen',
						'font-style': 'italic'
					},
					styleObj2: {
						'font-size': '30px'
					}

				}
			});
		</script>
	</body>
</html>
```



### 事件绑定
#### 指令v-on
*Vue使用`v-on`指令监听DOM事件,开发者可以将事件代码通过v-on指令绑定到DOM节点上,其简写为 `@`*
```html
<div id="app">
	<input type="button" value="简写:鼠标点击事件" @click="show" />
	<input type="button" value="鼠标点击事件" id="btn" v-on:click="show" />
	<input type="button" value="鼠标覆盖事件" id="btn" v-on:mouseover="show" />
</div>
<script>
	var v = new Vue({
		el: '#app',
		data: {
			message: 'v-on',
		},
	    methods: {
			show: function() {
				alert('Hi Vue!')
			}
		}
	});
</script>
```

#### 常见修饰符
*常用的事件修饰符如下所示 :*

|   名称   | 可用版本  | 可用事件 |                     说明                      |
| :------: | :-------: | :------: | :-------------------------------------------: |
|  .stop   |   所有    |   任意   |           当事件触发时,阻止事件冒泡           |
| .prevent |   所有    |   任意   |        当事件触发时,阻止元素的默认行为        |
| .capture |   所有    |   任意   |           当事件触发时,限制事件捕获           |
|  .self   |   所有    |   任意   |           限制事件仅作用于节点自身            |
|  .once   | 2.1.4以上 |   任意   |          事件被触发一次后既解除监听           |
| .passive | 2.1.4以上 |   滚动   | 移动端,限制事件永不调用`preventDefault()`方法 |

1. *`.stop`事件修饰符示例程序如下 :*
```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<script src="../resources/vue.js"></script>
		<title>.stop</title>
		<style>
			#app{
				height: 60px;
				width: 60px;
				background-color: lightgray;
			}
		</style>
	</head>
	<body>
		<div id="app" @click="divHandler">
			<input type="button" value="Click" @click.stop="btnHandler" />
		</div>
		<script>
			var vm = new Vue({
				el: '#app',
				methods: {
					divHandler: function() {
						console.log("触发'divHandler事件 !'")

					},
					btnHandler: function() {
						console.log("触发了'btnHandler事件 !'")
					}
				}
			});
		</script>
	</body>
</html>
```

2. *`.prevent`事件修饰符示例程序如下 :*
```html
<div id="app">
	<a href="https://yubuntu0109.github.io" @click.prevent="linkClick">my personal website ~</a>
</div>
<script>
	var vm = new Vue({
		el: '#app',
		methods: {
			linkClick: function() {
			console.log("触发了'linkClick'事件 !")
			}
		}
	});
</script>
```

3. *`.capture`事件修饰符示例程序如下 :*
```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<script src="../resources/vue.js"></script>
		<title>.capture</title>
		<style>
			#app{
				background-color: lightcyan;;
				height: 60px;
				width: 60px;
			}
		</style>
	</head>
	<body>
		<div id="app" @click.capture="divHandler">
			<input type="button" value="Click" @click="btnHandler" />
		</div>
		<script>
			var vm = new Vue({
				el: '#app',
				methods: {
					divHandler: function() {
						console.log("触发'divHandler事件 !'")

					},
					btnHandler: function() {
						console.log("触发了'btnHandler事件 !'")
					}
				}
			});
		</script>
	</body>
</html>
```

4. *`.self`事件修饰符示例程序如下 :*
```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<script src="../resources/vue.js"></script>
		<title>.self</title>
		<style>
			#app{
				height: 60px;
				width: 60px;
				background-color: lightgray;
			}
		</style>
	</head>
	<body>
		<div id="app" @click="divHandler">
			<input type="button" value="Click" @click.stop="btnHandler" />
		</div>
		<script>
			var vm = new Vue({
				el: '#app',
				data: {

				},
				methods: {
					divHandler: function() {
						console.log("触发'divHandler事件 !'")

					},
					btnHandler: function() {
						console.log("触发了'btnHandler事件 !'")
					}
				}
			});
		</script>
	</body>
</html>
```

5. *`.once`事件修饰符示例程序如下 :*
```html
<div id="app">
	<a href="https://yubuntu0109.github.io" @click.prevent.once="linkClick">my personal website ~</a>
</div>
<script>
	var vm = new Vue({
		el: '#app',
		methods: {
			linkClick: function() {
			console.log("触发了'linkClick'事件 !")
			}
		}
	});
</script>
```

#### 按键修饰符
*略 · · ·*
#### 组件修饰符
*略 · · ·*



### 双向绑定
*Vue实现数据绑定视图的基本原理可以参考我的这篇学习笔记哟 : demo.url*
#### 指令v-model
*可以使用`v-model`为可输入元素(input&textarea)创建双向数据绑定(`v-bind`只能实现数据的单向绑定),它会根据元素类型自动选取正确的方法来更新元素哟 ~*
```html
<div id="app">
	<h2>{{msg}}</h2>
	v-model : <input type="text" v-model="msg" />
</div>
<script>
    var vm = new Vue({
		el: '#app',
		data: {
			msg: 'stay hungray,stay foolish ~'
		}
	});
</script>
```

#### v-model与修饰符
| 修饰符  | 可用版本 |                           说明                           |
| :-----: | :------: | :------------------------------------------------------: |
|  .lay   |   所有   | 将用户输入的数据赋值于变量的时机由输入时延迟到数据改变时 |
| .number |   所有   |                自动转换用户输入为数值类型                |
|  .trim  |   所有   |              自动过滤用户输入的首尾空白字符              |

#### v-model与自定义组件
*略 · · ·*



### 条件渲染和列表渲染
#### 指令v-if与v-show
*`v-if`的使用方法并不复杂,只需要为元素挂上v-if指令即可,与之配套的还有`v-else-if`和`v-else`. 而`v-show`指令也可以用于实现条件渲染,不过它只是简单地切换元素的CSS属性 : display. 根据它们实现的机制来说 : `v-show`有更高的初始渲染开销,而`v-if`有更高的切换开销 .*

1. *`v-if`指令示例程序如下 :*
```html
<div id="app">
	<h2 v-if="order===0">v-if</h2>
	<h2 v-else-if="order===1">v-else-if</h2>
	<h2 v-else>v-else</h2>
	<button @click="toggleTitle">change title</button>
</div>
<script>
	let vm = new Vue({
		el: '#app',
		data: {
			order: 0,
		},
		methods: {
			toggleTitle() {
		    this.order = ++this.order % 3
			console.log('order vaule : ', this.order)
			}
		}
	});
</script>
```

2. *`v-show`示例程序如下 :*
```html
<div id="app">
    <h2 v-show="flag">v-show</h2>
	<button type="button" @click="flag=!flag">display(true) / display(false)</button>
</div>
<script>
	var vm = new Vue({
		el: '#app',
		data: {
			flag: true
		}
	});
</script>
```

#### 指令v-for
*`v-for`用于实现列表渲染,可以使用`item in items`或`item of items`的语法 .*

1. *使用`v-for`遍历普通数组 :*
```html
<div id="app">
	<p>array : {{list}}</p><hr>
	<p v-for="(item,i) in list">index:{{i}}--->element:{{item}}</p>
</div>
<script>
	var vm = new Vue({
		el: '#app',
		data: {
			list: [1, 2, 3]
		}
	});
</script>
```

2. *使用`v-for`遍历对象 :*
```html
<div id="app">
	<p v-for="(val,key,i) in user">INDEX:{{i}}--->{{key}}--->{{val}}</p>
</div>
<script>
	var vm = new Vue({
		el: '#app',
		data: {
			user: {
				id: 1,
				name: 'YUbuntu0109',
				gender: 'male',
				age: '21',
				email: '3083968068@qq.com'
			}
		}
	});
</script>
```

3. *使用`v-for`遍历对象数组 :*
```html
<div id="app">
	<p v-for="(user,i) in list">INDEX:{{i}}--->ID:{{user.id}}--->NAME:{{user.name}}</p>
</div>
<script>
	var vm = new Vue({
		el: '#app',
		data: {
			list: [{
				id: 1,
				name: 'demo-name-1'
				},
				{
				id: 2,
				name: 'demo-name-2'
				},
				{
				id: 3,
				name: 'demo-name-3'
				}
			]
		}
	});
</script>
```

#### 列表渲染中的key
*在使用`v-for`时,最好为每个迭代元素提供一个不重复的`key`. 因为当列表渲染被重新执行(数组内容发生变化)时,若不适用`key`,Vue会为数组成员就近复用已存在的DOM节点,如下图所示 :*

![](Vue-js的语法/Vue-for-withoutKey.png)

*当使用key时,`Vue会根据key的变化重新排列节点顺序,并移除key不存在的节点 !` 其实质是 : key的存在是为DOM节点标注了一个身份信息,继而让Vue有迹可循地追踪到数据对应的节点 ! 示例程序如下 :*
```html
<div id="app">
    <div>
        <label>ID : <input type="text" v-model="id"></label>
        <label>NAME : <input type="text" v-model="name"></label>
        <button @click="add">ADD</button>
    </div>
    <!-- Avoid using non-primitive value as key, use string/number value instead. -->
    <p v-for="user in users" v-bind:key="user.id">
        <input type="checkbox" />
        {{user.id}}--->{{user.name}}
    </p>
</div>
<script>
	var vm = new Vue({
	    el: '#app',
		data: {
			id: '',
			name: '',
			users: [{
				id: 1,
				name: 'demo-name-1'
			    },
			    {
				id: 2,
				name: 'demo-name-2'
		    	},
			    {
				id: 3,
				name: 'demo-name-3'
			    }
		    ]
	    },
		methods: {
		    add() {
	    	    //the elements to add to the front of the array. 
		        this.users.unshift({
				    id: this.id,
				    name: this.name
			    });
		    }
	    }
    });
</script>
```

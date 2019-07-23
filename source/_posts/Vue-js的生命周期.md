---
title: Vue.js的生命周期
date: 2019-07-20 21:56:19
tags: [Vue]
---

## 学习笔记 : 详解Vue.js的生命周期

### Vue实例的生命周期函数
*从Vue实例创建,运行,到销毁期间,总是伴随着各种各样的事件,这些事件统称为生命周期,至于生命周期钩子只不过是生命周期事件的别名而已. 其生命周期图如下所示 :*

![](Vue-js的生命周期/Vue-lifecycle.png)

#### 初始阶段
| 钩子函数 | 描述  | 数据  | 真实DOM | 项目中的应用 |
| :------: | :---: | :---: | :-----: | :----------: |
| beforeCreate | 表示组件创建前的准备工作,为事件的发布订阅和生命周期的开始做初始化 |  无  |  无  | 项目中一般不使用|    
|   created    |                         表示组件创建结束                      |  有  |  无  | 异步数据请求,然后可以进行一次默认数据的修改 |
| beforeMount  |                  表示组件装载前的准备工作(VDOM)                |  有  |  无  | 数据请求,它也可以进行一次数据修改    |
|   mounted    |                   组件挂载结束,真实DOM渲染完成                 |  有  |  有  | DOM操作,第三方库的实例化           |

- *`beforeCreate` : 在实例初始化之后,数据观测(data observer)和event/watcher事件配置之前被调用*
- *`created` : 实例已经创建完成之后被调用. 在这一步,实例已完成以下的配置 : 数据观测(data observer)属性初始化和方法的运算,watch/event事件回调. 然而挂载阶段还没开始,`$el`属性目前不可见*
- *`beforeMount` : 在挂载开始之前被调用,相关的render函数首次被调用*
- *`mounted` : `el`被新创建的`vm.$el`替换，并挂载到实例上去之后调用该钩子. 如果root实例挂载了一个文档内元素,当mounted被调用时vm.$el也在文档内*


#### 运行阶段
| 钩子函数 | 描述  | 数据  | 真实DOM | 项目中的应用 |
| :------: | :---: | :---: | :-----: | :----------: |
| beforeUpdate | 数据更新前的准备工作,数据修改才会执行  | 有(修改后的数据) |           有            | 生成新的VDOM,然后通过diff算法进行两次VDOM对比,没有太多的操作意义 |
|   updated    | 数据更新结束,通过render函数渲染真实DOM |       有       | 有(重新渲染后的真实DOM)   | 可以进行异步数据请求得到的DOM渲染的第三方库实例化 |

- *`beforeUpdate` : 数据更新时调用,发生在虚拟DOM重新渲染和打补丁之前. 你可以在这个钩子中进一步地更改状态,这不会触发附加的重渲染过程*
- *`updated` : 由于数据更改导致的虚拟DOM重新渲染和打补丁,在这之后会调用该钩子. 当这个钩子被调用时,组件DOM已经更新,所以你现在可以执行依赖于DOM的操作*


#### 销毁阶段
|   钩子函数    |                 描述                  |
| :-----------: | :-----------------------------------: |
| beforeDestroy | 组件即将销毁,准备调用`$destroy()`方法 |
|   destroyed   |             组件销毁结束              |

- *`beforeDestroy` : 实例销毁之前调用. 在这一步,实例仍然完全可用*
- *`destroyed` : Vue实例销毁后调用. 调用后,Vue实例指示的所有东西都会解绑定,所有的事件监听器会被移除,所有的子实例也会被销毁*



### 生命周期函数程序示例
*可通过下面这个简单实例来观察这些生命周期函数执行的时机哟 ~*
```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<script src="resources/vue.js"></script>
		<title>the life cycle of Vue</title>
	</head>
	<body>
		<div id="app">
			<h2>{{title}}</h2>
			<button @click="randomTitle()">Change title</button>
			<button @click="destoryVm()">destory VM</button>
		</div>
		<script>
			var vm = new Vue({
				el: '#app',
				data() {
					return {
						title: 'Hi Vue !'
					}
				},
				//methods将被混入到Vue实例中.可以直接通过VM实例访问这些方法,或者在指令表达式中使用.
				//方法中的this自动绑定为Vue实例.
				methods: {
					randomTitle: function() {
						//floor:returns the largest integer less than or equal to a number. 
						this.title = 'Hi ' + ['HTML', 'CSS', 'JS'][Math.floor(Math.random() * 2.99)]
					},
					destoryVm: function() {
						this.$destroy()
					}
				},
				//实例初始化后,数据观测和事件绑定之前
				beforeCreate() {
					//注:此时data和methods还未被初始化哟
					console.log('before create')
				},
				//实例初始化完成,挂载尚未开始时 
				created() {
					//注:此时data与methods已被初始化了哟
					console.log('created')
				},
				//挂载之前,render函数首先被调用时
				beforeMount() {
					//注:此时模板已在内存中编译完成,但尚未把模板渲染到页面中
					console.log('before mount')
				},
				//在实例挂载到DOM节点之后
				mounted() {
					//注:此时内存中的模板已真实挂载到页面中,继而完成页面的渲染
					console.log('mounted')
				},
				//数据更新时,在虚拟DOM状态变化之前
				beforeUpdate() {
					//注:数据被修改时则触发该事件,但此时页面上的数据并未更新哟
					console.log('before update')
				},
				//虚拟DOM被重新渲染之后
				updated() {
					//注:此时页面中的数据已经与data保持同步了哟
					console.log('updated')
				},
				//实例销毁之前,此时实例依然可用
				beforeDestroy() {
					console.log('before destory')
				},
				//实例销毁后,此时Vue实例及其子实例将完全解绑
				destroyed() {
					console.log('destoryed')
				}
			});
		</script>
	</body>
</html>
```
1. *刚打开页面,既Vue示例刚被创建并挂载到DOM上时,调用的钩子函数如下图所示 :*

![](Vue-js的生命周期\vue-lifecycle-created+mounted.PNG)

2. *点击`change title`按钮来改变标题数据,继而会触发视图更新,调用的钩子函数如下图所示 :*

![](Vue-js的生命周期\vue-lifecycle-updated.PNG)

3. *点击`destroy VM`按钮来销毁Vue实例,调用的钩子函数如下图所示 :*

![](Vue-js的生命周期\vue-lifecycle-destroyed.PNG)

4. *之后,尝试多次点击`chang title`按钮,发现视图不再响应数据的变化,继而证明此时节点上绑定的Vue实例已被销毁 !*

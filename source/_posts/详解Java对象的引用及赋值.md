---
title: 详解Java对象的引用及赋值
date: 2019-04-23 18:21:00
tags: [Java]
---

## 细节 : 详解`Java`中对象的引用及赋值

### 问题引入
1. *前言*
   
*前些天学习数据结构与算法时特意写了一篇名为`详解Linked-list的实现方式及其应用`的文章,其中`循环链表`的代码中就淋漓尽致地体现了Java中`对象`的引用与赋值*

2. *循环链表的实现*
```java
package pers.huangyuhui.linkedlist;

/**
 * @ClassName: CircularLinkedList
 * @Description: 操作循环链表
 * @author: HuangYuhui
 * @param <T>
 * @date: Apr 16, 2019 7:25:36 PM
 * 
 */
public class CircularLinkedList<T> {

	// get the length of the circular linked list
	public int getLength(ListNode<T> headNode) {
		int length = 0;
		ListNode<T> currentNode = headNode;
		while (currentNode != null) {
			currentNode = currentNode.getNext();
			length++;
			if (currentNode == headNode) {
				break;
			}
		}
		return length;
	}

	// traverse the node of the circular linked list
	public void traverseNode(ListNode<T> headNode) {
		System.out.println("\n### [headNode]-address : " + headNode + "\n");
		ListNode<T> currentNode = headNode;
		while (currentNode != null) {
			System.out.print(currentNode.getData() + " -> ");
			currentNode = currentNode.getNext();
			if (currentNode == headNode) {
				break;
			}
		}
		System.out.print("headNode(" + currentNode.getData() + ")\n");
	}

	// add new node at the tail of linked list
	public void insertAtListTail(ListNode<T> headNode, ListNode<T> newNode) {

		ListNode<T> currentNode = headNode;
		while (currentNode.getNext() != headNode) {
			//// 注意: 由于`currentNode`无变化,导致`currentNode.getNext() != headNode`继而进入进入死循环 !////
			// currentNode.setNext(currentNode.getNext());
			currentNode = currentNode.getNext();
		}
		newNode.setNext(newNode);
		if (headNode == null) {
			headNode = newNode;
		} else {
			newNode.setNext(headNode);
			currentNode.setNext(newNode);
		}
	}

	// add new node at the header of linked list
	public ListNode<T> insertAtListHeader(ListNode<T> headNode, ListNode<T> newNode) {

		ListNode<T> currentNode = headNode;
		while (currentNode.getNext() != headNode) {
			currentNode = currentNode.getNext();// 尾节点
		}
		newNode.setNext(newNode);// 指针指向自身
		if (headNode == null) {
			headNode = newNode;
		}
		newNode.setNext(headNode);
		currentNode.setNext(newNode);
		// 注意: 此时链表头结点已更新! 所以应该返回更新后的头节点继而避免遍历时出现死循环!!!
		headNode = newNode;
		return headNode;
	}

	// add the new node by the specified index
	public ListNode<T> insertNodeByIndex(ListNode<T> headNode, ListNode<T> newNode, int position) {

		if (headNode == null) {
			System.out.println("Error: the circular list is empty !");
			return null;
		} else if (position == 1) {
			insertAtListHeader(headNode, newNode);
		} else if (position == getLength(headNode)) {
			insertAtListTail(headNode, newNode);
		} else {
			ListNode<T> currentNode = headNode;
			ListNode<T> temp = headNode;
			for (int i = 0; i < position - 1; i++) {
				temp = currentNode;// 待插节点的前节点
				currentNode = currentNode.getNext();// 待插节点
			}
			temp.setNext(newNode);
			newNode.setNext(currentNode);
		}
		return headNode;
	}

	// delete the last node
	public void deleteLastNode(ListNode<T> headNode) {

		ListNode<T> temp = headNode;
		ListNode<T> currentNode = headNode;
		if (headNode == null) {
			System.err.println("Error: the circular list is empty !");
		}
		while (currentNode.getNext() != headNode) {
			temp = currentNode; // 尾节点的前一个节点
			currentNode = currentNode.getNext();
		}
		temp.setNext(headNode);
		currentNode = null;
	}

	// delete the header node
	public ListNode<T> deleteHeaderNode(ListNode<T> headNode) {
		ListNode<T> currentNode = headNode;
		if (headNode == null) {
			System.err.println("Error: the circular list is empty !");
			return null;
		}

		while (currentNode.getNext() != headNode) {
			// currentNode.setNext(currentNode.getNext());//死循环
			currentNode = currentNode.getNext();
		}
		currentNode.setNext(headNode.getNext());
		// 注意: 此时链表头结点已更新! 所以应该返回更新后的头节点继而避免遍历时出现死循环!!!
		headNode = headNode.getNext();
		return headNode;
	}

	// delete the node by the specified index
	public ListNode<T> deleteNodeByIndex(ListNode<T> headNode, int position) {

		if (headNode == null) {
			System.out.println("Error: the circular linked list is empty !");
			return null;
		} else if (position == 1) {
			deleteHeaderNode(headNode);
		} else if (position == getLength(headNode)) {
			deleteLastNode(headNode);
		} else {
			ListNode<T> currentNode = headNode;
			ListNode<T> temp = headNode;
			for (int i = 0; i < position - 1; i++) {
				temp = currentNode;// 待删节点的前节点
				currentNode = currentNode.getNext();// 待删节点
			}
			temp.setNext(currentNode.getNext());
		}

		return headNode;
	}

	// Test
	public static void main(String[] args) {

		// 初始化链表头结点
		CircularLinkedList<Integer> list = new CircularLinkedList<>();
		ListNode<Integer> headNode = new ListNode<>();
		headNode.setData(1);// 初始化链表头结点
		headNode.setNext(headNode);// 节点指针指向自身

		// 初始化待插入的链表节点
		ListNode<Integer> a = new ListNode<>();
		a.setData(2);
		ListNode<Integer> b = new ListNode<>();
		b.setData(3);
		ListNode<Integer> c = new ListNode<>();
		c.setData(4);
		ListNode<Integer> d = new ListNode<>();
		d.setData(100);
		ListNode<Integer> e = new ListNode<>();
		e.setData(101);
		ListNode<Integer> f = new ListNode<>();
		f.setData(0);

		// 向链表的尾部添加三个节点
		System.out.print("the origin node: ");
		list.insertAtListTail(headNode, a);
		list.insertAtListTail(headNode, b);
		list.insertAtListTail(headNode, c);
		list.traverseNode(headNode);
		System.out.println("the length of the list: " + list.getLength(headNode));

		// 向链表中添加两个头结点( 头结点被更新 )
		System.out.print("add two header node: ");
		ListNode<Integer> newHeadNode = list.insertAtListHeader(headNode, d);
		// 注意: 由于`头结点`已在`insertAtListHeader`中已更新所以要向`traverseNode`传入新的头结点
		ListNode<Integer> newHeadNode2 = list.insertAtListHeader(newHeadNode, e);
		list.traverseNode(newHeadNode2);
		System.out.println("the length of the list: " + list.getLength(headNode));

		// 在链表的指定位置上插入新的节点
		System.out.print("Insert the new node at position 3: ");
		list.insertNodeByIndex(headNode, f, 3);
		list.traverseNode(newHeadNode2);
		System.out.println("the length of the list: " + list.getLength(headNode));

		// 删除链表的尾节点
		System.out.print("delete the tail node:  ");
		list.deleteLastNode(newHeadNode2);
		list.traverseNode(newHeadNode2);
		System.out.println("the length of the list: " + list.getLength(headNode));

		// 注意: 由于`头结点`已在`deleteHeaderNode`中已更新所以要向`traverseNode`传入新的头结点对象
		ListNode<Integer> newHeadNode3 = list.deleteHeaderNode(newHeadNode2);
		System.out.print("delete the header node:  ");
		list.traverseNode(newHeadNode3);
		System.out.println("the length of the list: " + list.getLength(headNode));

		// 删除链表中指定位置的节点
		ListNode<Integer> newHeadNode4 = list.deleteNodeByIndex(newHeadNode3, 4);
		System.out.print("delete the fourth node: ");
		list.traverseNode(newHeadNode4);
		System.out.println("the length of the list: " + list.getLength(headNode));

	}
}
```

3. *程序运行结果*
```java
the origin node: 
### [headNode]-address : pers.huangyuhui.linkedlist.ListNode@16f65612

1 -> 2 -> 3 -> 4 -> headNode(1)
the length of the list: 4
add two header node: 
### [headNode]-address : pers.huangyuhui.linkedlist.ListNode@311d617d

101 -> 100 -> 1 -> 2 -> 3 -> 4 -> headNode(101)
the length of the list: 6
Insert the new node at position 3: 
### [headNode]-address : pers.huangyuhui.linkedlist.ListNode@311d617d

101 -> 100 -> 1 -> 2 -> 0 -> 3 -> 4 -> headNode(101)
the length of the list: 7
delete the tail node:  
### [headNode]-address : pers.huangyuhui.linkedlist.ListNode@311d617d

101 -> 100 -> 1 -> 2 -> 0 -> 3 -> headNode(101)
the length of the list: 6
delete the header node:  
### [headNode]-address : pers.huangyuhui.linkedlist.ListNode@7c53a9eb

100 -> 1 -> 2 -> 0 -> 3 -> headNode(100)
the length of the list: 5
delete the fourth node: 
### [headNode]-address : pers.huangyuhui.linkedlist.ListNode@7c53a9eb

100 -> 1 -> 2 -> 3 -> headNode(100)
the length of the list: 4
```


### 基本数据类型作为参数传递
1. *示例程序*
```java
public class test {

	public static void main(String[] args) {
		int i = 1;
		System.out.println("before change, i=" + i);
		change(i);
		System.out.println("after change, i=" + i);
	}

	private static void change(int i) {
		i = 5;
	}
}
```

2. *程序运行结果*
```java
before change, i=1
after change, i=1
```  

3. *结论*
   
*当`基本数据类型`作为参数传递时,传递时的是`实参的副本`,既传的是`值`,无论在函数中怎么操作这个副本,实参的值是不会被改变的.*



### 对象作为参数传递
1. *第一个示例程序*
```java
public class test {

	public static void main(String[] args) {

		StringBuffer sb = new StringBuffer("Hello");
		System.out.println("before change: " + sb);
		change(sb);
		System.out.println("after change: " + sb);
	}

	private static void change(StringBuffer stringBuffer) {
		stringBuffer.append(" world !");

	}
}
```

2. *程序运行结果*
```java
before change: Hello
after change: Hello world !
```

3. *探究结论*
*从上述程序运行结果可知`sb`所指的对象的值被改变了!那么我们是否就可以认为Java中的对象作为参数传递时,传递的是该对象的引用呢 `?` 那我们再来看第二个示例程序.*

4. *第二个示例程序*
```java
public class test {

	public static void main(String[] args) {

		StringBuffer sb = new StringBuffer("Hello");
		System.out.println("before change: " + sb);
		change(sb);
		System.out.println("after change: " + sb);
	}

	private static void change(StringBuffer stringBuffer) {
		stringBuffer = new StringBuffer("Hi");
		stringBuffer.append(" world !");

	}
}
```
5. *程序运行结果*
   
*如果上面的`推论`:(Java中对象作为参数传递时,实际传递的是该对象的引用)是正确的,那么在调用`change`函数后,原对象的值应该会被改变,既变为:`Hi World !`,但是,该程序的运行结果如下 !!!*
```java
before change: Hello
after change: Hello
```
*可知原对象(`sb`)的值并没有被改变,这是为什么呢? 下面让我们来`分析`一下其中的原因吧嘿嘿 ~*


6. 结论分析
   
*当我们执行`StringBuffer sb = new StringBuffer("Hello")`时,我们便创建了一个指向新建对象`new StringBuffer("Hello")`的引用`sb`,如下所示.*
```
sb ——————> [Hello]
```

*在`第二个示例程序`中,当我们调用`change`函数后,实际上,形参`stringBuffer`也指向了实参`sb`所指的对象! 如下所示.*
```java
sb ——————> [Hello] <—————— stringBuffer
```
*那么当我们执行`stringBuffer.append("world!")`后,便通过对象的引用(`stringBuffer`)修改了对象的值,使之修改成了: `Hello world !`,如下所示:*
```
sb ——————> [Hello world !] <—————— stringBuffer
```

*但是在`第二个示例程序`中的`change`函数中,我们又新建了一个对象:`new StringBuffer("Hi")`(该操作实际上是在内存中开辟了一块在原对象地址之外的`新区域`),这让形参`stringBuffer`实际指向了这个新建的对象,并将新对象的值设置为了`Hi world !`,如下所示:*
```
sb ——————> [Hello]
stringBuffer ——————> [Hi World !]
```
- *综上所述,可以得出结论: `在Java中,当对象作为参数传递时,实际上传递的是一份"引用"的拷贝 !`*


### 总结
*在Java中, `=` 不能看成一个赋值语句,因为它并不是把一个对象赋给另一个对象的过程,它的执行过程实质上是将右边对象的`地址`传给了左边的引用,使得左边的引用指向了右边的对象.Java表面上看起来没有指针,但它的引用实质上就是一个`指针`,引用里面存放的并不是对象,而是存放该对象的地址,使得该引用指向了该对象.*
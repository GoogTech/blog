---
title: 详解Linked list的实现方式及其应用
date: 2019-04-19 11:54:07
tags: [Java,Data Structures and Algorithms]
---

## 链表

### 链表的定义
*`链表`是一种物理存储单元上非连续、非顺序的存储结构,数据元素的逻辑顺序是通过链表中的指针链接次序实现的.链表由一系列`结点`(链表中每一个元素称为结点)组成,结点可以在运行时动态生成.每个结点包括两个部分: 一个是存储数据元素的`数据域`,另一个是存储下一个结点地址的`指针域`.*

### 链表的使用场景
1. 数据量较小.
2. 不需要预先知道数据规模.
3. 适应于频繁的插入操作.

### 链表的实现方式

#### 单向链表
1. *首先定义一个链表*
```java
package pers.huangyuhui.linkedlist;

/**
 * @ClassName: ListNode
 * @Description: 单向链表
 * @author: HuangYuhui
 * @date: Apr 15, 2019 8:59:50 PM
 * 
 */
public class SinglyListNode<E> {

	private E data;
	private SinglyListNode<E> next;// the pointer

	public E getData() {
		return data;
	}

	public void setData(E data) {
		this.data = data;
	}

	public SinglyListNode<E> getNext() {
		return next;
	}

	public void setNext(SinglyListNode<E> next) {
		this.next = next;
	}
}
```
2. *操作单向链表的示例程序*
```java
package pers.huangyuhui.linkedlist;

/**
 * @ClassName: SinglyLinkedList
 * @Description: 操作单向链表
 * @author: HuangYuhui
 * @date: Apr 15, 2019 8:54:46 PM
 * 
 */
public class SinglyLinkedList<T> {

	SinglyListNode<T> headNode;

	public void createLinkedList() {
		headNode = new SinglyListNode<T>();
		// headNode.setData(0);// attention: the header node is zero default
	}

	// get the header node of the linked list
	public T getHeaderNode() {
		return headNode.getData();
	}

	// get the length of the linked list
	public int getListLength(/* ListNode headNode */) {
		int length = 0;
		SinglyListNode<T> currentNode = headNode;
		while (currentNode != null) {
			currentNode = currentNode.getNext();
			length++;
		}
		return length;
	}

	// add a new node into the linked list by specified position
	public SinglyListNode<T> insertInLinked(SinglyListNode<T> nodeToInsert, int position) {

		if (headNode == null) {
			System.err.println("Error: the linked list is empty !");
			return nodeToInsert;
		}
		int size = getListLength();
		if (position > size + 1 || position < 1) {
			System.err.println("Position of node to insert is invalid. The vaild inputs are 1 to " + (size + 1));
			return headNode;
		}
		if (position == 1) {
			nodeToInsert.setNext(headNode);
			headNode = nodeToInsert;
			return nodeToInsert;
		} else {

			int count = 1;
			SinglyListNode<T> previousNode = headNode;
			while (count < position - 1) {
				previousNode = previousNode.getNext();// 待插节点的前节点
				count++;
			}

			SinglyListNode<T> currentNode = previousNode.getNext();// 待插节点的后节点
			nodeToInsert.setNext(currentNode);
			previousNode.setNext(nodeToInsert);
		}

		return headNode;
	}

	// delete the node by the specified position in the linked node
	public boolean deleteNodeFromLinkedList(/* ListNode headNode, */ int position) {
		int size = getListLength();
		if (position > size || position < 1) {
			System.err.println("Position of node to insert is invalid. The vaild inputs are 1 to" + (size + 1));
			return false;
		}
		if (position == 1) {
			SinglyListNode<T> currentNode = headNode.getNext();
			headNode = currentNode;
			return true;
		} else {// 删除中间或表尾结点
			int count = 1;
			SinglyListNode<T> previousNode = headNode.getNext();
			while (count < position) {
				previousNode = previousNode.getNext(); // 找到待删节点的前节点
				count++;
			}

			SinglyListNode<T> currentNode = previousNode.getNext();// 待删节点
			previousNode.setNext(currentNode.getNext());
			currentNode = null;
		}
		return true;
	}

	// delete the singly linked list
	public boolean destroyLinkedList(/* ListNode headNode */) {

		if (headNode == null) {
			System.err.println("Error: the singly linked list is empty !");
			return false;
		}
		System.out.print("delete the node: ");
		while (headNode != null) {
			System.out.print(headNode.getData() + " , ");
			headNode = headNode.getNext();
		}
		System.out.println();
		return true;
	}

	// Iterate through all the data in the linked list
	public boolean traverseNode( /* ListNode headNode */) {

		if (headNode == null) {
			System.err.println("Error: the linked node is empty !");
			return false;
		}
		SinglyListNode<T> currentNode = headNode;
		System.out.print("All of node: ");
		while (currentNode != null) {
			System.out.print(currentNode.getData() + " , ");
			currentNode = currentNode.getNext();
		}
		System.out.println();
		return true;
	}

	// Test
	public static void main(String[] args) {

		SinglyLinkedList<String> list = new SinglyLinkedList<String>();

		// 定义待插入链表的节点
		SinglyListNode<String> a = new SinglyListNode<String>();
		a.setData("A");
		SinglyListNode<String> b = new SinglyListNode<String>();
		b.setData("B");
		SinglyListNode<String> c = new SinglyListNode<String>();
		c.setData("C");

		// 向单向链表中添加新的节点
		list.createLinkedList();
		list.insertInLinked(a, 1);
		list.insertInLinked(b, 2);
		list.insertInLinked(c, 1);

		System.out.println("The size of linked list: " + list.getListLength());
		System.out.println("The header node : " + list.getHeaderNode());
		list.traverseNode();
		System.out.println("Delete the node which the position is first: " + list.deleteNodeFromLinkedList(1));
		list.traverseNode();
		System.out.println("Delete all of node: " + list.destroyLinkedList());
		System.out.println("The size of linked list: " + list.getListLength());
		list.traverseNode();

	}
}
```
3. *程序运行结果*
```java
The size of linked list: 4
The header node : C
All of node: C , A , B , null , 
Delete the node which the position is first: true
All of node: A , B , null , 
delete the node: A , B , null , 
Delete all of node: true
The size of linked list: 0
Error: the linked node is empty !
```

#### 双向链表
1. *定义链表*
```java
package pers.huangyuhui.linkedlist;

/**
 * @ClassName: BidirectionalLinkList
 * @Description: 双向链表
 * @author: HuangYuhui
 * @date: Apr 16, 2019 2:31:09 PM
 * 
 */
public class DoubleListNode<E> {

	private E data;
	private DoubleListNode<E> next;// the pointer
	private DoubleListNode<E> previous;

	public E getData() {
		return data;
	}

	public void setData(E data) {
		this.data = data;
	}

	public DoubleListNode<E> getNext() {
		return next;
	}

	public void setNext(DoubleListNode<E> next) {
		this.next = next;
	}

	public DoubleListNode<E> getPrevious() {
		return previous;
	}

	public void setPrevious(DoubleListNode<E> previous) {
		this.previous = previous;
	}
}
```
2. *操作双向链表的示例程序*
```java
package pers.huangyuhui.linkedlist;

/**
 * @ClassName: CircularList
 * @Description: 操作循环链表
 * @author: HuangYuhui
 * @date: Apr 16, 2019 2:28:27 PM
 * 
 */
public class DoubleLinkedList<T> {

	DoubleListNode<T> headNode;

	// initialize the circular list
	public void createCircularList() {
		headNode = new DoubleListNode<T>();// Attention: the header node is empty default
		// headNode.setData(0);
	}

	// get the length of the circular list
	public int getLength() {
		int length = 0;
		DoubleListNode<T> currentNode = headNode;
		while (currentNode != null) {
			currentNode = currentNode.getNext();
			length++;
		}

		return length;
	}

	// get the header node of the double linked list
	public T getHeaderNode() {
		if (headNode == null) {
			System.err.println("Error: the double linked list is empty !");
			return null;
		}
		return headNode.getData();
	}

	// add new node into the double linked list by the specified position
	public DoubleListNode<T> insertNode(DoubleListNode<T> newNode, int position) {
		if (headNode == null) {
			System.err.println("Error: the double linked list is empty !");
		}
		int size = getLength();
		if (position < 1 || position > size + 1) {
			System.err.println("Position of node to insert is invalid. The vaild inputs are 1 to " + (size + 1));
			return headNode;
		}

		if (position == 1) {
			newNode.setNext(headNode);
			headNode.setPrevious(newNode);
			headNode = newNode;// 更新头结点
			return headNode;
		} else {
			int count = 1;
			DoubleListNode<T> previousNode = headNode;
			while (count < position - 1) {
				previousNode = previousNode.getNext();
				count++;
			}
			DoubleListNode<T> currentNode = previousNode.getNext();// 找到待插位置的节点
			newNode.setNext(currentNode);
			if (currentNode != null) {// 如果待插位置的节点不是尾节点
				currentNode.setPrevious(newNode);
			}
			previousNode.setNext(newNode);
			newNode.setPrevious(previousNode);
		}
		return headNode;
	}

	// delete node by the spcified position in the double linked list
	public boolean deleteNode(int position) {
		if (headNode == null) {
			System.err.println("Error: the circular list is empty !");
			return false;
		}

		if (position == 1) {
			DoubleListNode<T> secondNode = headNode.getNext();
			secondNode.setPrevious(null);
			headNode = secondNode;// as the header node
		} else {
			int count = 1;
			//// `previousNode`与`headNode`操作的是同一个对象哟 ! ////
			DoubleListNode<T> previousNode = headNode;
			while (count < position - 1) {
				previousNode = previousNode.getNext();// 待删节点的前节点
				count++;
			}

			DoubleListNode<T> currentNode = previousNode.getNext();// 待删节点
			DoubleListNode<T> laterNode = currentNode.getNext();// 待删节点的后节点

			previousNode.setNext(laterNode);
			if (laterNode != null) {
				laterNode.setPrevious(previousNode);
				currentNode = null;
			}
		}
		return true;
	}

	// Iterate through all the data in the linked list
	public void traverseNode() {
		DoubleListNode<T> node = headNode;
		if (node == null) {
			System.err.println("Error: the circular list is empty !");
		}
		while (node != null) {
			System.out.println("the node: " + node.getData());
			node = node.getNext();
		}
	}

	// destroy the double linked list
	public void destroyList() {
		if (headNode == null) {
			System.out.println("Error: the circular list is empty !");
		}
		while (headNode != null) {
			System.out.println("delete the node: " + headNode.getData());
			headNode = headNode.getNext();
		}
	}

	// Test
	public static void main(String[] args) {

		// CircularList<Integer> list = new CircularList<Integer>();
		DoubleLinkedList<Character> list = new DoubleLinkedList<Character>();

		// 初始化待插节点
		DoubleListNode<Character> a = new DoubleListNode<Character>();
		a.setData('a');
		DoubleListNode<Character> b = new DoubleListNode<Character>();
		b.setData('b');
		DoubleListNode<Character> c = new DoubleListNode<Character>();
		c.setData('c');

		// 向双向链表中添加新节点
		list.createCircularList();
		list.insertNode(a, 1);
		list.insertNode(b, 2);
		list.insertNode(c, 3);

		System.out.println("-------- traverse --------");
		list.traverseNode();
		System.out.println("--------------------------");
		System.out.println("The header node: " + list.getHeaderNode());
		System.out.println("The length of the circular list: " + list.getLength());
		System.out.println("Delete the node which the position is one: " + list.deleteNode(1));
		System.out.println("-------- traverse --------");
		list.traverseNode();
		System.out.println("--------------------------");
		System.out.println("The header node: " + list.getHeaderNode());
		System.out.println("The length of the circular list: " + list.getLength());

		System.out.println();
		list.destroyList();
		System.out.println("The header node: " + list.getHeaderNode());
	}
}
```

3. *程序运行结果*
```java
-------- traverse --------
the node: a
the node: b
the node: c
the node: null
--------------------------
The header node: a
The length of the circular list: 4
Delete the node which the position is one: true
-------- traverse --------
the node: b
the node: c
the node: null
--------------------------
The header node: b
The length of the circular list: 3

delete the node: b
delete the node: c
delete the node: null
The header node: null
Error: the double linked list is empty !
``` 

#### 循环链表
1. *定义一个链表*
```java
package pers.huangyuhui.linkedlist;

/**
 * @ClassName: CircularListNode
 * @Description: 循环链表
 * @author: HuangYuhui
 * @date: Apr 17, 2019 9:52:23 AM
 * 
 */
public class ListNode<T> {

	private T data;
	private ListNode<T> next;

	public T getData() {
		return data;
	}

	public void setData(T data) {
		this.data = data;
	}

	public ListNode<T> getNext() {
		return next;
	}

	public void setNext(ListNode<T> next) {
		this.next = next;
	}

}
```

2. *操作循环链表的示例程序*
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

		// 向链表中添加两个头结点
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

		// 注意: 由于`头结点`已在`deleteHeaderNode`中已更新所以要向`traverseNode`传入新的头结点
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
the origin node: 1 -> 2 -> 3 -> 4 -> headNode(1)
the length of the list: 4
add two header node: 101 -> 100 -> 1 -> 2 -> 3 -> 4 -> headNode(101)
the length of the list: 6
Insert the new node at position 3: 101 -> 100 -> 1 -> 2 -> 0 -> 3 -> 4 -> headNode(101)
the length of the list: 7
delete the tail node:  101 -> 100 -> 1 -> 2 -> 0 -> 3 -> headNode(101)
the length of the list: 6
delete the header node:  100 -> 1 -> 2 -> 0 -> 3 -> headNode(100)
the length of the list: 5
delete the fourth node: 100 -> 1 -> 2 -> 3 -> headNode(100)
the length of the list: 4
```

### 推荐书籍
- *上述示例程序主要参考本书籍：《数据结构与算法经典问题解析》—— 纳拉辛哈·卡路曼希[著]*
- *很认真的说这是我大二至今看过的最好的一本关于数据结构与算法的书籍哟 ! 其针对不同问题提供多个具有不同复杂度的解决方案.兼顾自学教材和面试辅导的不同需求呢 !*

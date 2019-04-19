---
title: 详解Stack的实现方式及其应用
date: 2019-04-13 10:00:01
tags: [Java,Data Structures and Algorithms]
---

## 栈
### 栈的定义 : `栈(Stack)`是一个有序线性表,只能在表的一端(称为栈顶 : top)执行插入和删除操作.最后插入的元素将第一个被删除.所以,栈也称为后进先出(Last In Frist Out: `LIFO`)或先进后出(Fist In Last Out: `FILO`)线性表.
### 注意点 : 两个改变栈的操作都有专用名称,一个称为`入栈(psuh)`: 表示在栈中插入一个元素. 另一个称为`出栈(pop)`: 表示从栈中删除一个元素.试图对一个空栈执行出栈的操作称为`下溢(underflow)`. 试图对一个满栈执行入栈操作称为`溢出(overflow)`. 通常溢出和下溢均被认为是异常.

### 栈的应用
- *直接应用*
1. 符号匹配.
2. 中缀表达式转换为后缀表达式.
3. 计算后缀表达式.
4. 实现函数的调用(包括递归).
5. 求范围误差(极差).
6. 网页浏览器中已访问页面的历史记录(后退back按钮).
7. 文本编辑器中的撤销(undo)序列.
8. HTML和XML文件中的标签(tag)匹配.

- *间接应用*
1. 作为一个算法的辅助数据结构(例如: 树的遍历算法).
2. 其它数据结构的组件(例如: 模拟队列).


### 推荐学习方法
- *推荐小伙伴们一个数据结构可视化的网站,可以大大提高学习效率哟(っ•̀ω•́)っ✎⁾⁾⁾ [GO !](https://www.cs.usfca.edu/~galles/visualization/about.html)*
![ ](详解Stack的实现方式及其应用/ListStack.PNG)


### 栈的实现方式

1. 基于简单数组的实现栈 ：程序示例如下*
```java
/**
 * @ClassName: SimpleStack
 * @Description: 利用简单数组实现栈
 * @author: HuangYuhui
 * @param <T>
 * @date: Apr 10, 2019 4:15:47 PM
 * 
 */
public class SimArrayStack<T> {

	// the size of stack
	private int maxSize;
	// store the data
	private Object[] stackArray;
	// the top pointer of the stack
	private int topPointer;

	public SimArrayStack(int max) {

		maxSize = max;
		topPointer = -1;
		stackArray = new Object[maxSize];
	}

	// push new data into the stack
	public T push(T element) {
		stackArray[++topPointer] = element;
		return element;
	}

	// peek the top data in the stack
	public Object peek() {
		return stackArray[topPointer];
	}

	// determines whether the stack is empty
	public boolean isEmpty() {
		return topPointer == -1;
	}

	// pop the data in the stack
	public Object pop() {
		return stackArray[topPointer--];
	}

	// pop all of data in the stack
	public boolean popAll() {
		if (isEmpty()) {
			return false;
		}
		while (!isEmpty()) {
			System.out.println("The element to be poped : " + pop());
		}
		return true;
	}

	// Iterate through all the data in the stack
	public void traverseElement() {
		System.out.print("All of element in the stack : ");
		for (int i = 0; i < stackArray.length; i++) {
			if (i != stackArray.length - 1) {
				System.out.print(stackArray[i] + " , ");
			} else {
				System.out.print(stackArray[i]);
			}
		}
		System.out.println();
	}

	// Test
	public static void main(String[] args) {

		// SimpleStack<Character> stack2 = new SimpleStack<Character>(6);
		SimArrayStack<Integer> stack = new SimArrayStack<Integer>(6);
		stack.push(1);
		stack.push(2);
		stack.push(3);
		stack.push(4);
		stack.push(5);
		stack.push(6);

		stack.traverseElement();
		System.out.println("The element to be poped : " + stack.pop());
		System.out.println("The top element : " + stack.peek());
		System.out.println("Push a new element : " + stack.push(7));
		System.out.println("The top element : " + stack.peek());
		System.out.println("Determines whether the stack is empty : " + stack.isEmpty());
		System.out.println("Pop all of elements : " + stack.popAll());
		System.out.println("Determines whether the stack is empty : " + stack.isEmpty());

	}
}

/* ## The program running results are as follows :

All of element in the stack : 1 , 2 , 3 , 4 , 5 , 6
The element to be poped : 6
The top element : 5
Push a new element : 7
The top element : 7
Determines whether the stack is empty : false
The element to be poped : 7
The element to be poped : 5
The element to be poped : 4
The element to be poped : 3
The element to be poped : 2
The element to be poped : 1
Pop all of elements : true
Determines whether the stack is empty : true

 */

```
#### 局限性 : 栈的最大空间必须预先声明且不能改变.试图对一个满栈执行入栈时操作将产生一个针对简单数组这种特定实现栈方式的异常 !


2. *基于动态数组的实现栈 ：程序示例如下*
```java
/**
 * @ClassName: DynArrayStack
 * @Description: 利用动态数组实现栈
 * @author: HuangYuhui
 * @date: Apr 11, 2019 5:49:02 PM
 * 
 */
public class DynArrayStack<T> {

	private int topPointer;
	private int capacity;
	private Object[] array;

	public DynArrayStack() {
		topPointer = -1;
		capacity = 1;
		array = new Object[capacity];
	}

	// determines whether the stack is empty
	public boolean isEmpty() {
		return (topPointer == -1);
	}

	// determines whether the stack is full
	public boolean isStackFull() {
		return (topPointer == capacity - 1);// or return (topPointer==array.length);
	}

	// peek the top data of the stack
	public Object peek() {
		return array[topPointer];
	}

	// push a new data into the stack
	public T push(T element) {
		if (isStackFull()) {
			doubleStack();
		}
		array[++topPointer] = element;

		return element;
	}

	// double the size of the array
	public void doubleStack() {
		Object newArr[] = new Object[capacity * 2];
		System.arraycopy(array, 0, newArr, 0, capacity);
		capacity *= 2;
		array = newArr;
	}

	// pop the data from the stack
	public Object pop() {
		if (isEmpty()) {
			System.err.println("The Stack is  overflow !");
		}
		return array[topPointer--];
	}

	// pop all of data from the stack
	public boolean popAll() {
		if (isEmpty()) {
			return false;
		}
		while (!isEmpty()) {
			System.out.println("The element to be poped : " + pop());
		}
		return true;
	}

	// Iterate through all the data in the stack
	public void traverseElement() {
		System.out.print("all of element in the stack : ");
		for (int i = 0; i < array.length; i++) {
			if (i != array.length - 1) {
				System.out.print(array[i] + " , ");
			}
		}
		System.out.println();
	}

	// delete the stack
	public void deleteStack() {
		topPointer = -1;
	}

    // Test
	public static void main(String[] args) {

		DynArrayStack<Character> stack = new DynArrayStack<Character>();
		stack.push('a');
		stack.push('b');
		stack.push('c');
		stack.push('d');
		stack.push('e');
		stack.push('f');
		stack.push('g');

		stack.traverseElement();
		System.out.println("The element to be poped : " + stack.pop());
		System.out.println("The top element : " + stack.peek());
		System.out.println("Push a new element : " + stack.push('h'));
		System.out.println("The top element : " + stack.peek());
		System.out.println("Determines whether the stack is empty : " + stack.isEmpty());
		System.out.println("Pop all of elements : " + stack.popAll());
		System.out.println("Determines whether the stack is empty : " + stack.isEmpty());

	}
}

/*## The program running results are as follows :

all of element in the stack : a , b , c , d , e , f , g , 
The element to be poped : g
The top element : f
Push a new element : h
The top element : h
Determines whether the stack is empty : false
The element to be poped : h
The element to be poped : f
The element to be poped : e
The element to be poped : d
The element to be poped : c
The element to be poped : b
The element to be poped : a
Pop all of elements : true
Determines whether the stack is empty : true

 */

```
#### 注意 : 倍增太多可能导致`内存溢出` !
#### 上述程序中利用`重复倍增技术)`提高了程序的性能,其总时间开销`T(n) ≈ O(n)` . 相比采用: 当栈满时,每次将数组的大小增加`1`更加节省了`push`操作的总时间开销.其总时间开销`T(n) ≈ O(n²)` . 

3. *基于链表来实现栈 ：程序示例如下*
```java
/**
 * @ClassName: ListNode
 * @Description: 定义链表
 * @author: HuangYuhui
 * @date: Apr 11, 2019 7:01:35 PM
 * 
 */
public class ListNode<T> {

	private T data;
	private ListNode<T> next;

	public ListNode(T d) {
		this.data = d;
	}

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

	@Override
	public String toString() {
		return "[" + data + "]";
	}
}

```

```java
/**
 * @ClassName: ListStack
 * @Description: 利用链表实现栈
 * @author: HuangYuhui
 * @date: Apr 11, 2019 6:59:46 PM
 * 
 */
public class ListStack<E> {

	private ListNode<E> headNode;

	public ListStack() {
		this.headNode = new ListNode<E>(null);
	}

	// push a new node into the linked list
	public ListNode<E> push(E data) {
		if (headNode == null) {
			headNode = new ListNode<E>(data);
		} else if (headNode.getData() == null) {
			headNode.setData(data);// initialize header node.
		} else {
			ListNode<E> newNode = new ListNode<E>(data); // create a new node.
			newNode.setNext(headNode);// connect to the header node.
			headNode = newNode;// set the new node to header node.

		}
		return headNode;
	}

	// returns the top node of the linked list
	public E top() {
		if (headNode == null) {
			return null;
		} else {
			return headNode.getData();
		}
	}

	// pop top node in the linked list
	public E pop() {
		if (headNode == null) {
			throw new EmptyStackException();
		} else {
			E node = headNode.getData();
			headNode = headNode.getNext();// Reset the header node.
			return node;
		}
	}

	// pop all of nodes in the linked list
	public boolean popAll() {
		if (headNode == null) {
			throw new EmptyStackException();
		}
		while (!isEmpty()) {
			System.out.println("Pop all of nodes : " + pop());
		}
		if (isEmpty()) {
			return true;
		}
		return true;
	}

	// determine whether the linked list is empty
	public boolean isEmpty() {
		if (headNode == null) {
			return true;
		} else {
			return false;
		}
	}

	// Iterate through all the node in the linked list
	public void getNodeByLoop(ListNode<E> head) {

		if (isEmpty()) {
			throw new EmptyStackException();
		}

		System.out.print("All of nodes of the linked list:  ");
		while (head != null) {
			if (head.getNext() != null) {
				System.out.print(head.getData() + " , ");
			} else {
				System.out.print(head.getData());
			}
			head = head.getNext();
		}
		System.out.println();
	}

	// destroy the linked list
	public boolean destroyStack(/* ListNode<E> head */) {

		ListNode<E> auxilaryNode = null, iterator = headNode;
		while (iterator != null) {
			auxilaryNode = iterator.getNext();
			iterator = null;
			iterator = auxilaryNode;

			headNode = iterator;// Set the node to be deleted as the header node.
		}
		if (auxilaryNode == null) {
			return true;
		}
		return false;
	}

	// Test
	public static void main(String[] args) {

		// ListStack<Integer> stack = new ListStack<Integer>();
		ListStack<String> stack = new ListStack<String>();
		stack.push("A");
		stack.push("B");
		stack.push("C");
		stack.push("D");
		stack.push("E");
		stack.push("F");
		stack.push("G");

		stack.getNodeByLoop(stack.headNode);
		System.out.println("The node to be poped : " + stack.pop());
		System.out.println("The top node : " + stack.top());
		System.out.println("Push a new node : " + stack.push("H"));
		System.out.println("The top node : " + stack.top());
		System.out.println("Determines whether the stack is empty : " + stack.isEmpty());
		System.out.println("Pop all of nodes(success ?) : " + stack.popAll());
		System.out.println("Determines whether the stack is empty : " + stack.isEmpty());
		System.out.println("Push a new node : " + stack.push("I"));
		System.out.println("Push a new node : " + stack.push("J"));
		System.out.println("Push a new node : " + stack.push("K"));
		System.out.println("Destroy the stack(success ?) : " + stack.destroyStack());
		System.out.println("Determines whether the stack is empty : " + stack.isEmpty());

	}

}

/*## The program running results are as follows :

All of nodes of the linked list:  G , F , E , D , C , B , A
The node to be poped : G
The top node : F
Push a new node : [H]
The top node : H
Determines whether the stack is empty : false
Pop all of nodes : H
Pop all of nodes : F
Pop all of nodes : E
Pop all of nodes : D
Pop all of nodes : C
Pop all of nodes : B
Pop all of nodes : A
Pop all of nodes(success ?) : true
Determines whether the stack is empty : true
Push a new node : [I]
Push a new node : [J]
Push a new node : [K]
Destroy the stack(success ?) : true
Determines whether the stack is empty : true

 */

```

### 栈的各种实现方法的比较
1. **递增策略和倍增策略的比较**
- *通过分析完成`n`个`push`操作的总时间开销`T(n)`来比较递增策略和倍增策略的区别.从长度为`1`的数组表示的空栈开始,一次`push`操作的平摊时间等于一组`push`操作的总时间开销的平均值.记为 : `T(n)/n`*
- *`递增策略` : 实现`push`操作的平摊时间开销为`O(n)[O(n²)/n]` .*
- *`倍增策略` : 实现`push`操作的平摊时间开销为`O(n)[O(n)/n]` .*

2. **基于数组实现和基于链表实现的比较**
#### (a) ：基于数组实现的栈
- *各个操作都是常数时间开销.*
- *每隔一段时间倍增操作的开销过大.*
- *(从空栈开始)`n`个操作的任意序列的平摊时间开销为`O(n)`.*
#### (b) ：基于链表实现的栈
- *栈规模的增加和减少都是很简洁.*
- *各个操作都是常数时间开销.*
- *每个操作都要使用额外的空间和时间开销来处理指针.*


### 栈的应用
1. *举例`1` ：将用户输入的字符反转*

- *首先定义一个基于简单数组的栈*
```java
/**
 * @ClassName: SimpleStack
 * @Description: 栈
 * @author: HuangYuhui
 * @date: Apr 10, 2019 8:50:56 PM
 * 
 */
public class SimpleStack {

	// 栈的大小
	private int maxSize;
	// 存储栈元素
	private char[] stackArray;
	// 栈顶指针
	private int topPointer;

	public SimpleStack(int max) {

		maxSize = max;
		topPointer = -1;
		stackArray = new char[maxSize];
	}

	// 逐个向栈中压入元素
	public void push(char c) {
		stackArray[++topPointer] = c;
	}

	// 逐个弹出栈中元素
	public char pop() {
		return stackArray[topPointer--];
	}

	// 弹出栈中所有的元素
	public void popAll() {
		while (!isEmpty()) {
			System.out.println("pop element : " + pop());
		}
	}

	// 查看栈顶元素
	public char peek() {
		return stackArray[topPointer];
	}

	// 判断栈是否为空
	public boolean isEmpty() {
		return topPointer == -1;
	}
}

```

- *利用栈来反转字符*
```java
/**
 * @ClassName: MyStack
 * @Description: 利用栈来反转字符串
 * @author: HuangYuhui
 * @date: Apr 10, 2019 3:46:40 PM
 * 
 */
public class ReversalString {

	private String input;
	private String output;

	public ReversalString(String in) {
		this.input = in;
	}

	// 反转字符
	public String doReversal() {

		int stackSize = input.length();
		SimpleStack stack = new SimpleStack(stackSize);

		// 将字符逐个压入栈中
		for (int i = 0; i < input.length(); i++) {
			char in = input.charAt(i);
			stack.push(in);
		}

		// 将字符逐个从栈中取出
		output = "";
		while (!stack.isEmpty()) {
			char out = stack.pop();
			output += out;
		}

		return output;
	}
}

```

- *接收用户输入的字符并测试*
```java
/**
 * @ClassName: ReversalStringTest
 * @Description: 测试反转字符程序
 * @author: HuangYuhui
 * @date: Apr 10, 2019 8:53:24 PM
 * 
 */

public class ReversalStringTest {

	static BufferedReader bufferedReader;

	@Test
	public void reversalTest() throws IOException {
		String input, output;

		while (true) {
			System.out.println("Please enter a string : ");
			// System.out.flush();
			input = getString();
			if (input.equals("exit")) {
				bufferedReader.close();
				break;
			}

			// 将字符串反转
			ReversalString reversal = new ReversalString(input);
			output = reversal.doReversal();

			System.out.println("Reversed : " + output);
		}
	}

	@Test
	@Ignore
	// 测试BufferedString中的控制字符反转的方法
	public void BufferedStringTest() {

		StringBuffer stringBuffer = new StringBuffer("reverse");

		System.err.println("Reversed : " + stringBuffer.reverse());

	}

	// 通过缓冲流中的'readLine'方法高效读入用户输入的数据
	public static String getString() {

		String s = null;
		bufferedReader = new BufferedReader(new InputStreamReader(System.in));
		try {
			s = bufferedReader.readLine();
		} catch (IOException e) {
			e.printStackTrace();
		}
		return s;
	}
}

```
- *程序运行结果如下 :*
```java
Please enter a string : 
my qq: 3083968068

Reversed : 8608693803 :qq ym

Please enter a string : 
exit
```

2. *举例 ：检查用户输入的运算符(括号匹配)*

- *首先定义一个基于简单数组的栈(同上,略写)*
- *利用栈中入栈和出栈操作匹配括号符*
```java
/**
 * @ClassName: BracketChecker
 * @Description: 匹配括号
 * @author: HuangYuhui
 * @date: Apr 10, 2019 9:58:36 PM
 * 
 */
public class BracketChecker {

	private String input;

	public BracketChecker(String in) {

		this.input = in;
	}

	public void chekc() {

		boolean flag_a = false;
		boolean flag_b = false;
		int stackSize = input.length();
		SimpleStack stack = new SimpleStack(stackSize);

		for (int i = 0; i < input.length(); i++) {
			char in = input.charAt(i);

			switch (in) {
			case '{':
			case '(':
			case '[':
			case '<':
				stack.push(in);
				break;
			case '}':
			case ')':
			case ']':
			case '>':
				// 括号匹配
				if (!stack.isEmpty()) {
					char out = stack.pop();
					if ((in == '}' && out != '{') || (in == ')' && out != '(') || (in == ']' && out != '[')
							|| (in == '>' && out != '<')) {
						System.err.println("error : " + in + " at " + i);
					} else {
						flag_a = true;
					}
				} else {
					System.err.println("error : " + in + " at " + i);
					flag_a = false;
				}

				break;
			// 只检查括号
			default:
				break;
			}
		}

		// 如果匹配成功,循环结束后栈中理应为空.
		if (!stack.isEmpty()) {
			System.out.println("Error : missing right delimiter.");
		} else {
			flag_b = true;
		}

		if (flag_a && flag_b) {
			System.out.println("Success !");
		}
	}
}

```

- *接收用户输入的运算符并测试匹配程序*
```java
/**
 * @ClassName: BracketCheckerTest
 * @Description: 测试匹配括号符程序
 * @author: HuangYuhui
 * @date: Apr 10, 2019 9:58:55 PM
 * 
 */
public class BracketCheckerTest {
	
	static BufferedReader bufferedReader;
	
	public static void main(String[] args) throws IOException {
		
		String input;
		
		while(true) {
			System.out.println("Please enter containing delimiters : ");
			System.out.flush();
			input = getString();
			if(input.equals("exit")) {
				bufferedReader.close();
				break;
			}
			
			BracketChecker checker = new BracketChecker(input);
			checker.chekc();
		}
	}

	// 通过缓冲流中的'readLine'方法高效读入用户的数据.
	public static String getString() {

		String s = null;
		bufferedReader = new BufferedReader(new InputStreamReader(System.in));
		try {
			s = bufferedReader.readLine();
		} catch (IOException e) {
			e.printStackTrace();
		}
		return s;
	}
}
```
- *程序运行结果如下 :*
```java
Please enter containing delimiters : 
{[<a>b]c)d}efg
error : ) at 8
error : } at 10

Please enter containing delimiters : 
{([<a>b]c)defg
Error : missing right delimiter.

Please enter containing delimiters : 
{([<a>b]cd}efg
error : } at 10
Error : missing right delimiter.

Please enter containing delimiters : 
{([<a>b]c)d}efg
Success !

Please enter containing delimiters : 
exit
```
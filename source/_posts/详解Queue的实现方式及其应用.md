---
title: 详解Queue的实现方式及其应用
date: 2019-08-31 21:37:20
tags: [Java,Data Structures and Algorithms]
---

## 学习笔记 : 详解Queue的实现方式及其应用

### 什么是队列
*队列是一种只能在一端插入( 队尾 ),在另外一端删除( 堆首 )的有序线性表. 队列中第一个插入的元素也是第一个被删除的元素. 所以,队列是一种先进先出( FIFO,First In First Out )或后进后出( LILO,Last In Last Out )线性表.*


### 队列的应用
1. 直接相关的应用
* 操作系统根据( 具有相同优先级的 )任务到达的顺序调度任务( 例如,打印队列 )
* 模拟现实世界中的队列,如售票柜台前的队伍,或者任何需要先来先服务的场景
* 多道程序设计
* 异步数据传输( 文件输入输出,管道,套接字 )

2. 间接相关的应用
* 作为算法的辅助数据结构
* 其他数据结构的组件


### 队列的实现方式
*队列抽象数据类型( 与栈类似 )有多种实现方式,下面是常用的方法 :*
* 基于简单循环数组的实现方法
* 基于动态循环数据的实现方法
* 基于链表的实现方法
 
#### 简单循环数组的实现方法
```java
package pers.huangyuhui.queue;


import java.util.Arrays;

/**
 * @project: Data structures and algorithms
 * @description: 基于简单循环数组实现队列
 * @author: 黄宇辉
 * @date: 8/31/2019-9:59 PM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public class ArrayQueue {

    private int front;
    private int rear;
    private int capacity;
    private int[] array;

    public ArrayQueue(int size) {
        this.capacity = size;
        front = rear = -1;
        array = new int[size];
    }

    public static ArrayQueue createQueue(int size) {
        return new ArrayQueue(size);
    }

    public boolean isEmpty() {
        return (front == -1);
    }

    public boolean isFull() {
        return ((rear + 1) % capacity == front);
    }

    public void traverse() {
        System.out.println(Arrays.toString(array));
    }

    public int getQueueSize() {
        if (front == -1) {
            return 0;
        }
        int size = (capacity - front + rear + 1) % capacity;
        if (size == 0) {
            return capacity;
        } else {
            return size;
        }
    }

    //入队
    public void enQueue(int data) {
        if (isFull()) {
            System.err.println("error : the queue is overflow");
        } else {
            rear = (rear + 1) % capacity;
            array[rear] = data;
            if (front == -1) {
                front = rear;
            }
            System.out.println("enter the data into the queue successfully : " + data);
        }
    }

    //出队
    public int deQueue() {
        int data = 0;
        if (isEmpty()) {
            System.err.println("error : the queue is empty");
        } else {
            data = array[front];
            array[front] = 0;
            if (front == rear) {
                front = rear = -1;
            } else {
                front = (front + 1) % capacity;
            }
            System.out.println("delete the data from the queue successfully : " + data);
        }
        return data;
    }

    //test
    public static void main(String[] args) {
        ArrayQueue queue = ArrayQueue.createQueue(5);
        //enter
        queue.enQueue(1);
        queue.enQueue(2);
        queue.enQueue(3);
        queue.enQueue(4);
        queue.enQueue(5);
        System.out.println("the size of queue : " + queue.getQueueSize());
        System.out.print("traverse the queue : ");
        queue.traverse();
        //delete
        queue.deQueue();
        queue.deQueue();
        queue.deQueue();
        queue.deQueue();
        queue.deQueue();
        System.out.println("the size of queue : " + queue.getQueueSize());
        System.out.print("traverse the queue : ");
        queue.traverse();
    }

}
```

*程序运行结果如下 :*
```java
enter the data into the queue successfully : 1
enter the data into the queue successfully : 2
enter the data into the queue successfully : 3
enter the data into the queue successfully : 4
enter the data into the queue successfully : 5
the size of queue : 5
traverse the queue : [1, 2, 3, 4, 5]

delete the data from the queue successfully : 1
delete the data from the queue successfully : 2
delete the data from the queue successfully : 3
delete the data from the queue successfully : 4
delete the data from the queue successfully : 5
the size of queue : 0
traverse the queue : [0, 0, 0, 0, 0]
```


#### 动态循环数据的实现方法
```java
package pers.huangyuhui.queue;

import java.util.Arrays;

/**
 * @project: Data structures and algorithms
 * @description: 基于动态循环数组实现队列
 * @author: 黄宇辉
 * @date: 9/1/2019-8:59 AM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public class DynArrayQueue {

    private int front;
    private int rear;
    private int capacity;
    private int[] array;

    private DynArrayQueue() {
        capacity = 1;
        front = rear = -1;
        array = new int[1];
    }

    public static DynArrayQueue createDynArrayQueue() {
        return new DynArrayQueue();
    }

    public boolean isEmpty() {
        return (front == -1);
    }

    public boolean isFull() {
        return ((rear + 1) % capacity == front);
    }

    public void traverse() {
        System.out.println(Arrays.toString(array));
    }

    public int getQueueSize() {
        if (front == -1) {
            return 0;
        }
        int size = (capacity - front + rear + 1) % capacity;
        if (size == 0) {
            return capacity;
        } else {
            return size;
        }
    }


    //扩容
    private void resizeQueue() {
        int initCapacity = capacity;
        capacity *= 2;
        int[] oldArray = array;
        array = new int[this.capacity];
        for (int i = 0; i < oldArray.length; i++) {
            array[i] = oldArray[i];
        }
        //important
        if (rear < front) {
            for (int i = 0; i < front; i++) {
                array[i + initCapacity] = this.array[i];
                array[i] = 0;
            }
            rear += initCapacity;
        }

    }

    //入队
    public void enQueue(int data) {
        if (isFull()) {
            resizeQueue();
        }
        rear = (rear + 1) % capacity;
        array[rear] = data;
        if (front == -1) {
            front = rear;
        }
        System.out.println("enter a data into the queue successfully : " + data);
    }

    //出队
    public int deQueue() {
        int data = -1;
        if (isEmpty()) {
            System.err.println("error : the queue is empty");
        } else {
            data = array[front];
            array[front] = 0; //delete the data
            if (front == rear) {
                front = rear = -1;

            } else {
                front = (front + 1) % capacity;
            }
            System.out.println("delete a data from the queue successfully : " + data);
        }
        return data;
    }


    //test
    public static void main(String[] args) {
        DynArrayQueue dynArrayQueue = DynArrayQueue.createDynArrayQueue();
        //enter
        dynArrayQueue.enQueue(1);
        dynArrayQueue.enQueue(2);
        dynArrayQueue.enQueue(3);
        dynArrayQueue.enQueue(4);
        dynArrayQueue.enQueue(5);
        System.out.println("the size of queue : " + dynArrayQueue.getQueueSize());
        System.out.print("traverse the queue : ");
        dynArrayQueue.traverse();
        //delete
        dynArrayQueue.deQueue();
        dynArrayQueue.deQueue();
        dynArrayQueue.deQueue();
        dynArrayQueue.deQueue();
        dynArrayQueue.deQueue();
        System.out.println("the size of queue : " + dynArrayQueue.getQueueSize());
        System.out.print("traverse the queue : ");
        dynArrayQueue.traverse();


    }

}
```

*程序运行结果如下所示 :*
```java
enter a data into the queue successfully : 1
enter a data into the queue successfully : 2
enter a data into the queue successfully : 3
enter a data into the queue successfully : 4
enter a data into the queue successfully : 5
the size of queue : 5
traverse the queue : [1, 2, 3, 4, 5, 0, 0, 0]

delete a data from the queue successfully : 1
delete a data from the queue successfully : 2
delete a data from the queue successfully : 3
delete a data from the queue successfully : 4
delete a data from the queue successfully : 5
the size of queue : 0
traverse the queue : [0, 0, 0, 0, 0, 0, 0, 0]
```


#### 链表的实现方法
```java
package pers.huangyuhui.queue;

//定义一个单向链表
class LLNode {
    private int data;
    private LLNode next;

    public LLNode(int data) {
        this.data = data;
    }

    public int getData() {
        return data;
    }

    public void setData(int data) {
        this.data = data;
    }

    public LLNode getNext() {
        return next;
    }

    public void setNext(LLNode next) {
        this.next = next;
    }
}


/**
 * @project: Data structures and algorithms
 * @description: 基于链表实现队列
 * @author: 黄宇辉
 * @date: 9/1/2019-9:19 AM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public class LLQueue {

    private LLNode frontNode; //represents headNode
    private LLNode rearNode; //represents lastNode

    public LLQueue() {
        this.frontNode = null;
        this.rearNode = null;
    }

    public static LLQueue createQueue() {
        return new LLQueue();
    }

    public boolean isEmpty() {
        return (frontNode == null);
    }

    public int getQueueSize() {
        int length = 0;
        LLNode currentNode = frontNode;
        while (currentNode != null) {
            length++;
            currentNode = currentNode.getNext();
        }
        return length;
    }

    //遍历
    public void traverse() {
        if (isEmpty()) {
            System.err.println("error : the queue is empty !");
            return;
        }
        LLNode currentNode = frontNode;
        System.out.print("the all data of queue : [ ");
        while (currentNode != null) {
            System.out.print(currentNode.getData() + " ,");
            currentNode = currentNode.getNext();
        }
        System.out.print(" ]\n");
    }

    //入队
    public void enQueue(int data) {
        LLNode newNode = new LLNode(data);
        if (rearNode != null) {
            rearNode.setNext(newNode);
        }
        rearNode = newNode;
        if (frontNode == null) {
            frontNode = rearNode;
        }
        System.out.println("enter the data into the queue successfully : " + data);
    }

    //出队
    public int deQueue() {
        int data = 0;
        if (isEmpty()) {
            System.err.println("error : the queue is empty");
        } else {
            data = frontNode.getData();
            frontNode = frontNode.getNext();
        }
        System.out.println("delete the data from the queue successfully : " + data);
        return data;
    }


    //test
    public static void main(String[] args) {
        LLQueue queue = LLQueue.createQueue();
        //enter
        queue.enQueue(1);
        queue.enQueue(2);
        queue.enQueue(3);
        queue.enQueue(4);
        queue.enQueue(5);
        System.out.println("the size of the queue : " + queue.getQueueSize());
        System.out.print("traverse the queue : ");
        queue.traverse();
        //delete
        queue.deQueue();
        queue.deQueue();
        queue.deQueue();
        queue.deQueue();
        queue.deQueue();
        System.out.println("the size of the queue : " + queue.getQueueSize());
        System.out.print("traverse the queue : ");
        queue.traverse();

    }

}
```

*程序运行结果如下所示 :*
```java
enter the data into the queue successfully : 1
enter the data into the queue successfully : 2
enter the data into the queue successfully : 3
enter the data into the queue successfully : 4
enter the data into the queue successfully : 5
the size of the queue : 5
traverse the queue : the all data of queue : [ 1 ,2 ,3 ,4 ,5 , ]

delete the data from the queue successfully : 1
delete the data from the queue successfully : 2
delete the data from the queue successfully : 3
delete the data from the queue successfully : 4
delete the data from the queue successfully : 5
the size of the queue : 0
traverse the queue : error : the queue is empty !
```



*参考书籍( 已纠正书中错误的示例代码 ) : 《数据结构与算法经典问题解析 - 原书第2版》*
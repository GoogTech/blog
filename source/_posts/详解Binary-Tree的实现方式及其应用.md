---
title: 详解Binary Tree的实现方式及其应用
date: 2019-09-01 21:10:45
tags: [Java,Data Structures and Algorithms]
---

## 学习笔记 : 详解Binary Tree的实现方式及其应用

### 什么是二叉树
*如果一棵树中的每个结点有0,1或者2个孩子结点,那么这颗树就称为二叉树. 空树也是一棵有效的二叉树. 一个二叉树可以看作由根节点和两颗不相交的子树( 分别称为左子树和右子树 )组成. 其类型可分为 : 严格二叉树,满二叉树,完全二叉树.*


### 二叉树的应用
* 编译器中的表达式树
* 用于数据压缩算法中的赫夫曼编码树
* 支持在集合中查找,插入和删除,其平均时间复杂度为 O(logn) 的二叉树搜索(或称为查找)树( BST )
* 优先队列( PQ ),它支持以对数时间( 最坏情况下 )对集合中的最小( 或最大 )数据进行搜索和删除


### 二叉树的遍历
*根据实体( 结点 )处理顺序不同,可以定义不同的遍历方法. 其分类如下 :*
* 前序遍历( DLR ) : 根节点 ——> 左子树 ——> 右子树
* 中序遍历( LDR ) : 左子树 ——> 根节点 ——> 右子树
* 后序遍历( LRD ) : 左子树 ——> 右子树 ——> 根节点
* 层次遍历 : 这种方法由图的广度优先遍历方法启发得来

#### 程序实例
1. *定义二叉树的结构*
```java
package pers.huangyuhui.tree;

/**
 * @project: data structures and algorithms
 * @description: 定义二叉树的结构
 * @author: 黄宇辉
 * @date: 9/1/2019-2:59 PM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public class BinaryTreeNode {

    private int data;
    private BinaryTreeNode left;
    private BinaryTreeNode right;

    public BinaryTreeNode() {
    }

    public BinaryTreeNode(int data) {
        this.data = data;
    }

    public BinaryTreeNode(int data, BinaryTreeNode left, BinaryTreeNode right) {
        this.data = data;
        this.left = left;
        this.right = right;
    }

    public int getData() {
        return data;
    }

    public void setData(int data) {
        this.data = data;
    }

    public BinaryTreeNode getLeft() {
        return left;
    }

    public void setLeft(BinaryTreeNode left) {
        this.left = left;
    }

    public BinaryTreeNode getRight() {
        return right;
    }

    public void setRight(BinaryTreeNode right) {
        this.right = right;
    }
}
```

2. *定义单向链表的结构*
```java
package pers.huangyuhui.tree;

/**
 * @project: data structures and algorithms
 * @description: 定义单向链表的结构
 * @author: 黄宇辉
 * @date: 9/1/2019-5:09 PM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public class LLNode<T> {
    private T data;
    private LLNode next;

    public LLNode() {
    }

    public LLNode(T data) {
        this.data = data;
    }

    public T getData() {
        return data;
    }

    public void setData(T data) {
        this.data = data;
    }

    public LLNode getNext() {
        return next;
    }

    public void setNext(LLNode next) {
        this.next = next;
    }
}
```

3. *基于单向链表来实现一个栈*
```java
package pers.huangyuhui.tree;

/**
 * @project: data structures and algorithms
 * @description: 基于单向链表实现栈
 * @author: 黄宇辉
 * @date: 9/1/2019-3:10 PM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public class LLStack<T> {

    private LLNode<T> headNode;

    public LLStack() {
        this.headNode = new LLNode<>();
    }

    public boolean isEmpty() {
        return headNode == null;
    }

    //栈顶
    public T top() {
        if (headNode == null) {
            return null;
        } else {
            return headNode.getData();
        }
    }

    //入栈
    public void push(T data) {
        if (isEmpty()) {
            headNode = new LLNode<>(data);
        } else if (headNode.getData() == null) {
            headNode.setData(data);
        } else {
            var llNode = new LLNode<>(data);
            llNode.setNext(headNode);
            headNode = llNode;
        }
    }

    //出栈
    public T pop() {
        if (isEmpty()) {
            return null;
        } else {
            var data = headNode.getData();
            headNode = headNode.getNext();
            return data;
        }
    }

}
```

4. *分别测试二叉树的前序,中序,后序遍历方式*
```java
package pers.huangyuhui.tree;


/**
 * @project: data structures and algorithms
 * @description: 二叉树的遍历方式
 * @author: 黄宇辉
 * @date: 9/1/2019-2:24 PM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public class BinaryTree {

    public static BinaryTreeNode initBinaryTree() {
        BinaryTreeNode root = new BinaryTreeNode();
        root.setData(1); //根节点
        root.setLeft(new BinaryTreeNode(2, new BinaryTreeNode(4), new BinaryTreeNode(5))); //左子树
        root.setRight(new BinaryTreeNode(3, new BinaryTreeNode(6), new BinaryTreeNode(7))); //右子树
        return root;
    }

    //递归前序遍历
    public void preOrder(BinaryTreeNode root) {
        if (root != null) {
            System.out.print(root.getData() + ", ");
            preOrder(root.getLeft());
            preOrder(root.getRight());
        }
    }

    //非递归前序遍历
    public void preOrderNonRecursive(BinaryTreeNode root) {
        if (root == null) {
            System.err.println("error : the tree is empty");
        }
        var stack = new LLStack<BinaryTreeNode>();
        while (true) {
            while (root != null) {
                System.out.print(root.getData() + ", ");
                stack.push(root);
                root = root.getLeft();
            }
            if (stack.isEmpty()) {
                break;
            }
            root = stack.pop();
            root = root.getRight();
        }
    }

    //递归中序遍历
    public void inOrder(BinaryTreeNode root) {
        if (root != null) {
            inOrder(root.getLeft());
            System.out.print(root.getData() + ", ");
            inOrder(root.getRight());
        }
    }

    //非递归中序遍历
    public void inOrderNonRecursive(BinaryTreeNode root) {
        if (root == null) {
            System.err.println("error : the tree is empty");
        }
        var stack = new LLStack<BinaryTreeNode>();
        while (true) {
            while (root != null) {
                stack.push(root);
                root = root.getLeft();
            }
            if (stack.isEmpty()) {
                break;
            }
            root = stack.pop();
            System.out.print(root.getData() + ", ");
            root = root.getRight();
        }
    }

    //递归后序遍历
    public void postOrder(BinaryTreeNode root) {
        if (root != null) {
            postOrder(root.getLeft());
            postOrder(root.getRight());
            System.out.print(root.getData() + ", ");
        }
    }

    //非递归后序遍历
    public void postNonRecursive(BinaryTreeNode root) {
        //······
    }

    //test
    public static void main(String[] args) {
        BinaryTree binaryTree = new BinaryTree();
        BinaryTreeNode binaryTreeNode = BinaryTree.initBinaryTree();
        System.out.println("------ 递归前序遍历 ------");
        binaryTree.preOrder(binaryTreeNode); //1,2,4,5,3,6,7
        System.out.println("\n------ 非递归前序遍历 ------");
        binaryTree.preOrderNonRecursive(binaryTreeNode);
        System.out.println("\n------ 递归中序遍历 ------");
        binaryTree.inOrder(binaryTreeNode); //4,2,5,1,6,3,7
        System.out.println("\n------ 非递归中序遍历 ------");
        binaryTree.inOrderNonRecursive(binaryTreeNode);
        System.out.println("\n------ 递归后序遍历 ------");
        binaryTree.postOrder(binaryTreeNode); //4,5,2,6,7,3,1
    }

}
```

5. *程序运行结果如下所示 :*
```java
------ 递归前序遍历 ------
1, 2, 4, 5, 3, 6, 7, 
------ 非递归前序遍历 ------
1, 2, 4, 5, 3, 6, 7, 
------ 递归中序遍历 ------
4, 2, 5, 1, 6, 3, 7, 
------ 非递归中序遍历 ------
4, 2, 5, 1, 6, 3, 7, 
------ 递归后序遍历 ------
4, 5, 2, 6, 7, 3, 1, 
```



*参考书籍( 已完善书中的示例代码 ) : 《数据结构与算法经典问题解析 - 原书第2版》*
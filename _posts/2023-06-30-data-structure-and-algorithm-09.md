---
layout:     post
title:      "数据结构与算法 09-树结构基础"
subtitle:   
date:       2023-07-09 16:00
author:     "Chenghao"
header-img: "img/post-bg-2015.jpg"
tags:
    - 数据结构与算法
    - Java
---

> 笔记摘自：尚硅谷 | B站视频链接：<https://www.bilibili.com/video/BV1E4411H73v/>

## 1.1 二叉树

### 1.1.1 为什么需要树这种数据结构

1) 数组存储方式的分析

优点：通过下标方式访问元素，速度快。对于有序数组，还可使用二分查找提高检索速度。 缺点：如果要检索具体某个值，或者插入值(按一定顺序)会整体移动，效率较低 [示意图] 画出操作示意图：

<img src = "/Users/chenghaozhang/Library/CloudStorage/OneDrive-Personal/MyBlog/chzhang1821.github.io/img/data_structure_imgs/树01.png" alt = "树01" style = "zoom: 50%" />

2) 链式存储方式的分析

优点：在一定程度上对数组存储方式有优化(比如：插入一个数值节点，只需要将插入节点，链接到链表中即可， 删除效率也很好)。 缺点：在进行检索时，效率仍然较低，比如(检索某个值，需要从头节点开始遍历) 【示意图】 操作示意图：

<img src = "/Users/chenghaozhang/Library/CloudStorage/OneDrive-Personal/MyBlog/chzhang1821.github.io/img/data_structure_imgs/树02.png" alt = "树02" style = "zoom: 50%" />

3) 树存储方式的分析

能提高数据存储，读取的效率, 比如利用 二叉排序树(Binary Sort Tree)，既可以保证数据的检索速度，同时也 可以保证数据的插入，删除，修改的速度。【示意图,后面详讲】 案例: [7, 3, 10, 1, 5, 9, 12]

<img src = "/Users/chenghaozhang/Library/CloudStorage/OneDrive-Personal/MyBlog/chzhang1821.github.io/img/data_structure_imgs/树03.png" alt = "树03" style = "zoom: 50%" />

### 1.1.2 树示意图

<img src = "/Users/chenghaozhang/Library/CloudStorage/OneDrive-Personal/MyBlog/chzhang1821.github.io/img/data_structure_imgs/树04.png" alt = "树04" style = "zoom: 50%" />

### 1.1.3 二叉树的概念

1) 树有很多种，每个节点最多只能有两个子节点的一种形式称为二叉树。

2) 二叉树的子节点分为左节点和右节点

3) 示意图

<img src = "/Users/chenghaozhang/Library/CloudStorage/OneDrive-Personal/MyBlog/chzhang1821.github.io/img/data_structure_imgs/树05.png" alt = "树05" style = "zoom: 50%" />

4) 如果该二叉树的所有叶子节点都在最后一层，并且结点总数 = $ 2^n -1$,  n 为层数，则我们称为满二叉树。

<img src = "/Users/chenghaozhang/Library/CloudStorage/OneDrive-Personal/MyBlog/chzhang1821.github.io/img/data_structure_imgs/树06.png" alt = "树06" style = "zoom: 50%" />

5) 如果该二叉树的所有叶子节点都在最后一层或者倒数第二层，而且最后一层的叶子节点在左边连续，倒数第二 层的叶子节点在右边连续，我们称为完全二叉树

<img src = "/Users/chenghaozhang/Library/CloudStorage/OneDrive-Personal/MyBlog/chzhang1821.github.io/img/data_structure_imgs/树07.png" alt = "树07" style = "zoom: 50%" />

### 1.1.4 二叉树遍历的说明

使用前序，中序和后序对下面的二叉树进行遍历.

1) 前序遍历: 先输出父节点，再遍历左子树和右子树

2) 中序遍历: 先遍历左子树，再输出父节点，再遍历右子树

3) 后序遍历: 先遍历左子树，再遍历右子树，最后输出父节点

4) 小结: 看输出父节点的顺序，就确定是前序，中序还是后序



### 1.1.5 二叉树遍历应用实例(前序,中序,后序)

应用实例的说明和思路

<img src = "/Users/chenghaozhang/Library/CloudStorage/OneDrive-Personal/MyBlog/chzhang1821.github.io/img/data_structure_imgs/树08.png" alt = "树08" style = "zoom: 50%" />

代码实现：

```java
package com.chenghao;

/**
 * Hello world!
 */
public class BinaryTree {
    public static void main(String[] args) {
        // 创建二叉树
        BinaryTree1 binaryTree = new BinaryTree1();
        HeroNode root = new HeroNode(1, "宋江");
        HeroNode node2 = new HeroNode(2, "吴用");
        HeroNode node3 = new HeroNode(3, "卢俊义");
        HeroNode node4 = new HeroNode(4, "林冲");
        HeroNode node5 = new HeroNode(5, "关胜");

        root.setLeft(node2);
        root.setRight(node3);
        node3.setRight(node4);
        node3.setLeft(node5);

        binaryTree.setRoot(root);

        System.out.println("前序遍历：");
        binaryTree.preOrder(); // 1, 2, 3, 5, 4

        System.out.println("中序遍历");
        binaryTree.infixOrder(); // 2, 1, 5, 3, 4

        System.out.println("后序遍历");
        binaryTree.postOrder(); // 2, 5, 4, 3, 1
    }
}

class BinaryTree1 {
    private HeroNode root;

    public void setRoot(HeroNode root) {
        this.root = root;
    }

    // 前序
    public void preOrder() {
        if (this.root != null) {
            this.root.preOrder();
        } else {
            System.out.println("当前二叉树为空，无法遍历");
        }
    }

    public void infixOrder() {
        if (this.root != null) {
            this.root.infixOrder();
        } else {
            System.out.println("当前二叉树为空，无法遍历");
        }
    }

    public void postOrder() {
        if (this.root != null) {
            this.root.postOrder();
        } else {
            System.out.println("当前二叉树为空，无法遍历");
        }
    }
}

class HeroNode {
    private int no;
    private String name;
    private HeroNode left;
    private HeroNode right;
    public HeroNode(int no, String name) {
        this.no = no;
        this.name = name;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public HeroNode getLeft() {
        return left;
    }

    public void setLeft(HeroNode left) {
        this.left = left;
    }

    public HeroNode getRight() {
        return right;
    }

    public void setRight(HeroNode right) {
        this.right = right;
    }

    public int getNo() {
        return no;
    }

    public void setNo(int no) {
        this.no = no;
    }

    @Override
    public String toString() {
        return "HeroNode{" +
                "no=" + no +
                ", name='" + name + '\'' +
                '}';
    }

    // 前序遍历
    public void preOrder() {
        System.out.println(this);
        if (this.left != null) {
            this.left.preOrder();
        }
        // 递归向右子树前序遍历
        if (this.right != null) {
            this.right.preOrder();
        }
    }

    // 中序遍历
    public void infixOrder() {
        if (this.left != null) {
            this.left.infixOrder();
        }
        // 输出父节点
        System.out.println(this);
        // 向右
        if (this.right != null) {
            this.right.infixOrder();
        }
    }

    // 后序遍历
    public void postOrder() {
        if (this.left != null) {
            this.left.postOrder();
        }
        if (this.right != null) {
            this.right.postOrder();
        }
        System.out.println(this);
    }
}
```



### 1.1.6 二叉树-查找指定节点

要求

1) 请编写前序查找，中序查找和后序查找的方法。

2) 并分别使用三种查找方式，查找 heroNO = 5 的节点

3) 并分析各种查找方式，分别比较了多少次

4) 思路分析图解

<img src = "/Users/chenghaozhang/Library/CloudStorage/OneDrive-Personal/MyBlog/chzhang1821.github.io/img/data_structure_imgs/树09.png" alt = "树09" style = "zoom: 50%" />

5. 代码实现

```java
public class BinaryTree {
    public static void main(String[] args) {
        // 创建二叉树
        BinaryTree1 binaryTree = new BinaryTree1();
        HeroNode root = new HeroNode(1, "宋江");
        HeroNode node2 = new HeroNode(2, "吴用");
        HeroNode node3 = new HeroNode(3, "卢俊义");
        HeroNode node4 = new HeroNode(4, "林冲");
        HeroNode node5 = new HeroNode(5, "关胜");

        root.setLeft(node2);
        root.setRight(node3);
        node3.setRight(node4);
        node3.setLeft(node5);

        binaryTree.setRoot(root);

        System.out.println("前序遍历：");
        binaryTree.preOrder(); // 1, 2, 3, 5, 4

        System.out.println("中序遍历");
        binaryTree.infixOrder(); // 2, 1, 5, 3, 4

        System.out.println("后序遍历");
        binaryTree.postOrder(); // 2, 5, 4, 3, 1

        System.out.println("前序查找");
        HeroNode result = binaryTree.preOrderSearch(5);
        if (result != null) {
            System.out.printf("找到了，信息为 no = %d name = %s\n", result.getNo(), result.getName());
        } else {
            System.out.printf("没有找到 no = %d 的英雄\n", 5);
        }

        System.out.println("中序查找");
        result = binaryTree.infixOrderSearch(5);
        if (result != null) {
            System.out.printf("找到了，信息为 no = %d name = %s\n", result.getNo(), result.getName());
        } else {
            System.out.printf("没有找到 no = %d 的英雄\n", 5);
        }

        System.out.println("后序查找");
        result = binaryTree.postOrderSearch(5);
        if (result != null) {
            System.out.printf("找到了，信息为 no = %d name = %s\n", result.getNo(), result.getName());
        } else {
            System.out.printf("没有找到 no = %d 的英雄\n", 5);
        }
    }
}

class BinaryTree1 {
    private HeroNode root;

    public void setRoot(HeroNode root) {
        this.root = root;
    }

    // 前序
    public void preOrder() {
        if (this.root != null) {
            this.root.preOrder();
        } else {
            System.out.println("当前二叉树为空，无法遍历");
        }
    }

    public void infixOrder() {
        if (this.root != null) {
            this.root.infixOrder();
        } else {
            System.out.println("当前二叉树为空，无法遍历");
        }
    }

    public void postOrder() {
        if (this.root != null) {
            this.root.postOrder();
        } else {
            System.out.println("当前二叉树为空，无法遍历");
        }
    }

    public HeroNode preOrderSearch(int no) {
        HeroNode res = null;
        if (this.root != null) {
            res = this.root.preOrderSearch(no);
        }
        return res;
    }

    public HeroNode infixOrderSearch(int no) {
        HeroNode res = null;
        if (this.root != null) {
            res = this.root.infixOrderSearch(no);
        }
        return res;
    }

    public HeroNode postOrderSearch(int no) {
        HeroNode res = null;
        if (this.root != null) {
            res = this.root.postOrderSearch(no);
        }
        return res;
    }
}

class HeroNode {
    private int no;
    private String name;
    private HeroNode left;
    private HeroNode right;
    public HeroNode(int no, String name) {
        this.no = no;
        this.name = name;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public HeroNode getLeft() {
        return left;
    }

    public void setLeft(HeroNode left) {
        this.left = left;
    }

    public HeroNode getRight() {
        return right;
    }

    public void setRight(HeroNode right) {
        this.right = right;
    }

    public int getNo() {
        return no;
    }

    public void setNo(int no) {
        this.no = no;
    }

    @Override
    public String toString() {
        return "HeroNode{" +
                "no=" + no +
                ", name='" + name + '\'' +
                '}';
    }

    // 前序遍历
    public void preOrder() {
        System.out.println(this);
        if (this.left != null) {
            this.left.preOrder();
        }
        // 递归向右子树前序遍历
        if (this.right != null) {
            this.right.preOrder();
        }
    }

    // 中序遍历
    public void infixOrder() {
        if (this.left != null) {
            this.left.infixOrder();
        }
        // 输出父节点
        System.out.println(this);
        // 向右
        if (this.right != null) {
            this.right.infixOrder();
        }
    }

    // 后序遍历
    public void postOrder() {
        if (this.left != null) {
            this.left.postOrder();
        }
        if (this.right != null) {
            this.right.postOrder();
        }
        System.out.println(this);
    }

    // 前序查找
    public HeroNode preOrderSearch(int no) {
        if (this.no == no) {
            return this;
        }
        HeroNode resNode = null;
        if (this.left != null) {
            resNode = this.left.preOrderSearch(no);
        }
        if (resNode != null) {
            return resNode;
        }

        if (this.right != null) {
            resNode = this.right.preOrderSearch(no);
        }
        return resNode;
    }

    // 中序查找
    public HeroNode infixOrderSearch(int no) {
        HeroNode resNode = null;
        if (this.left != null) {
            resNode = this.left.infixOrderSearch(no);
        }
        if (resNode != null) {
            return resNode;
        }

        if (this.no == no) {
            return this;
        }

        if (this.right != null) {
            resNode = this.right.infixOrderSearch(no);
        }
        return resNode;
    }

    // 后序查找
    public HeroNode postOrderSearch(int no) {
        HeroNode resNode = null;
        if (this.left != null) {
            resNode = this.left.postOrderSearch(no);
        }
        if (resNode != null) {
            return resNode;
        }

        if (this.right != null) {
            resNode = this.right.postOrderSearch(no);
        }

        if (resNode != null) {
            return resNode;
        }

        if (this.no == no) {
            return this;
        }
        return resNode;
    }
}
```



### 1.1.7 二叉树-删除节点

要求

1) 如果删除的节点是叶子节点，则删除该节点

2) 如果删除的节点是非叶子节点，则删除该子树.

3) 测试，删除掉 5 号叶子节点 和 3 号子树.

4) 完成删除思路分析

<img src = "/Users/chenghaozhang/Library/CloudStorage/OneDrive-Personal/MyBlog/chzhang1821.github.io/img/data_structure_imgs/树10.png" alt = "树10" style = "zoom: 50%" />

5. 代码实现

```java
public class BinaryTree {
    public static void main(String[] args) {
        // 创建二叉树
        BinaryTree1 binaryTree = new BinaryTree1();
        HeroNode root = new HeroNode(1, "宋江");
        HeroNode node2 = new HeroNode(2, "吴用");
        HeroNode node3 = new HeroNode(3, "卢俊义");
        HeroNode node4 = new HeroNode(4, "林冲");
        HeroNode node5 = new HeroNode(5, "关胜");

        root.setLeft(node2);
        root.setRight(node3);
        node3.setRight(node4);
        node3.setLeft(node5);

        binaryTree.setRoot(root);

        System.out.println("删除节点前， 前序遍历");
        binaryTree.preOrder();
        System.out.println("删除节点后，前序遍历");
        binaryTree.delNode(3);
        binaryTree.preOrder();

    }
}

class BinaryTree1 {
    private HeroNode root;

    public void setRoot(HeroNode root) {
        this.root = root;
    }

    // 前序
    public void preOrder() {
        if (this.root != null) {
            this.root.preOrder();
        } else {
            System.out.println("当前二叉树为空，无法遍历");
        }
    }

    public void infixOrder() {
        if (this.root != null) {
            this.root.infixOrder();
        } else {
            System.out.println("当前二叉树为空，无法遍历");
        }
    }

    public void postOrder() {
        if (this.root != null) {
            this.root.postOrder();
        } else {
            System.out.println("当前二叉树为空，无法遍历");
        }
    }

    public HeroNode preOrderSearch(int no) {
        HeroNode res = null;
        if (this.root != null) {
            res = this.root.preOrderSearch(no);
        }
        return res;
    }

    public HeroNode infixOrderSearch(int no) {
        HeroNode res = null;
        if (this.root != null) {
            res = this.root.infixOrderSearch(no);
        }
        return res;
    }

    public HeroNode postOrderSearch(int no) {
        HeroNode res = null;
        if (this.root != null) {
            res = this.root.postOrderSearch(no);
        }
        return res;
    }

    public void delNode(int no) {
        if (root != null) {
            if (root.getNo() == no) {
                root = null;
            } else {
                root.delNode(no);
            }
        } else {
            System.out.println("这是一个空二叉树");
        }
    }
}

class HeroNode {
    private int no;
    private String name;
    private HeroNode left;
    private HeroNode right;
    public HeroNode(int no, String name) {
        this.no = no;
        this.name = name;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public HeroNode getLeft() {
        return left;
    }

    public void setLeft(HeroNode left) {
        this.left = left;
    }

    public HeroNode getRight() {
        return right;
    }

    public void setRight(HeroNode right) {
        this.right = right;
    }

    public int getNo() {
        return no;
    }

    public void setNo(int no) {
        this.no = no;
    }

    @Override
    public String toString() {
        return "HeroNode{" +
                "no=" + no +
                ", name='" + name + '\'' +
                '}';
    }

    // 前序遍历
    public void preOrder() {
        System.out.println(this);
        if (this.left != null) {
            this.left.preOrder();
        }
        // 递归向右子树前序遍历
        if (this.right != null) {
            this.right.preOrder();
        }
    }

    // 中序遍历
    public void infixOrder() {
        if (this.left != null) {
            this.left.infixOrder();
        }
        // 输出父节点
        System.out.println(this);
        // 向右
        if (this.right != null) {
            this.right.infixOrder();
        }
    }

    // 后序遍历
    public void postOrder() {
        if (this.left != null) {
            this.left.postOrder();
        }
        if (this.right != null) {
            this.right.postOrder();
        }
        System.out.println(this);
    }

    // 前序查找
    public HeroNode preOrderSearch(int no) {
        if (this.no == no) {
            return this;
        }
        HeroNode resNode = null;
        if (this.left != null) {
            resNode = this.left.preOrderSearch(no);
        }
        if (resNode != null) {
            return resNode;
        }

        if (this.right != null) {
            resNode = this.right.preOrderSearch(no);
        }
        return resNode;
    }

    // 中序查找
    public HeroNode infixOrderSearch(int no) {
        HeroNode resNode = null;
        if (this.left != null) {
            resNode = this.left.infixOrderSearch(no);
        }
        if (resNode != null) {
            return resNode;
        }

        if (this.no == no) {
            return this;
        }

        if (this.right != null) {
            resNode = this.right.infixOrderSearch(no);
        }
        return resNode;
    }

    // 后序查找
    public HeroNode postOrderSearch(int no) {
        HeroNode resNode = null;
        if (this.left != null) {
            resNode = this.left.postOrderSearch(no);
        }
        if (resNode != null) {
            return resNode;
        }

        if (this.right != null) {
            resNode = this.right.postOrderSearch(no);
        }

        if (resNode != null) {
            return resNode;
        }

        if (this.no == no) {
            return this;
        }
        return resNode;
    }

    public void delNode(int no) {
        if (this.left != null && this.left.no == no) {
            this.left = null;
            return;
        }
        if (this.right != null && this.right.no == no) {
            this.right = null;
            return;
        }
        if (this.left != null) {
            this.left.delNode(no);
        }
        if (this.right != null) {
            this.right.delNode(no);
        }
    }
}

```



### 1.1.8 二叉树-删除节点

思考题(课后练习)

1) 如果要删除的节点是非叶子节点，现在我们不希望将该非叶子节点为根节点的子树删除，需要指定规则, 假如 规定如下:

2) 如果该非叶子节点 A 只有一个子节点 B， 则子节点 B 替代节点 A

3) 如果该非叶子节点 A 有左子节点 B 和右子节点 C， 则让左子节点 B 替代节点 A。

4) 请大家思考，如何完成该删除功能, 老师给出提示.(课后练习)

5) 后面在讲解 二叉排序树时，在给大家讲解具体的删除方法

<img src = "/Users/chenghaozhang/Library/CloudStorage/OneDrive-Personal/MyBlog/chzhang1821.github.io/img/data_structure_imgs/树11.png" alt = "树11" style = "zoom: 50%" />



## 1.2 顺序存储二叉树

### 1.2.1 顺序存储二叉树的概念

* 基本说明 

  从数据存储来看，数组存储方式和树的存储方式可以相互转换，即数组可以转换成树，树也可以转换成数组， 看右面的示意图。

<img src = "/Users/chenghaozhang/Library/CloudStorage/OneDrive-Personal/MyBlog/chzhang1821.github.io/img/data_structure_imgs/树12.png" alt = "树12" style = "zoom: 50%" />

* 要求:

1) 右图的二叉树的结点，要求以数组的方式来存放 arr : [1, 2, 3, 4, 5, 6, 6]

2) 要求在遍历数组 arr 时，仍然可以以前序遍历，中序遍历和后序遍历的方式完成结点的遍历

* 顺序存储二叉树的特点:

1) 顺序二叉树通常只考虑完全二叉树

2) 第 n 个元素的左子节点在数组中的下标为 2 * n + 1

3) 第 n 个元素的右子节点在数组中的下标为 2 * n + 2

4) 第 n 个元素的父节点在数组中的下标为 (n-1) / 2

5) n : 表示二叉树中的第几个元素(按 0 开始编号如图所示)

### 1.2.2 顺序存储二叉树遍历

```java
package com.chenghao;

import java.util.Arrays;

public class ArrBinaryTree {
    public static void main(String[] args) {
        int[] arr = {1, 2, 3, 4, 5, 6, 7};
        ArrBinaryTree1 binaryTree = new ArrBinaryTree1(arr);
        binaryTree.preOrder(0);
    }
}

class ArrBinaryTree1 {
    private int[] arr;

    public ArrBinaryTree1(int[] arr) {
        this.arr = arr;
    }

    public void preOrder(int index) {
        // 如果数组为空，或者 arr.length = 0
        if (arr == null || arr.length == 0) {
            System.out.println("数组为空，不能按照二叉树的前序遍历");
        }
        // 输出当前这个数
        System.out.println(arr[index]);
        // 向左递归遍历
        if ((index * 2 + 1) < arr.length) {
            preOrder(2 * index + 1);
        }
        if ((index * 2 + 2) < arr.length) {
            preOrder(2 * index + 2);
        }
    }
}
```



### 1.2.3 顺序存储二叉树应用实例

八大排序算法中的堆排序，就会使用到顺序存储二叉树， 关于堆排序，我们放在<<树结构实际应用>> 章节讲解。

## 1.3 线索化二叉树

### 1.3.1 先看一个问题

将数列

{1, 3, 6, 8, 10, 14} 构建成一颗二叉树，n+1=7

<img src = "/Users/chenghaozhang/Library/CloudStorage/OneDrive-Personal/MyBlog/chzhang1821.github.io/img/data_structure_imgs/树13.png" alt = "树13" style = "zoom: 50%"/>

问题分析:

1) 当我们对上面的二叉树进行中序遍历时，数列为 {8, 3, 10, 1, 14, 6}

2) 但是 6, 8, 10, 14 这几个节点的 左右指针，并没有完全的利用上.

3) 如果我们希望充分的利用 各个节点的左右指针， 让各个节点可以指向自己的前后节点,怎么办?

4) 解决方案-线索二叉树



### 1.3.2 线索二叉树基本介绍

1) n 个结点的二叉链表中含有 n+1 【公式 2n-(n-1)=n+1】 个空指针域。利用二叉链表中的空指针域，存放指向 该结点在某种遍历次序下的前驱和后继结点的指针（这种附加的指针称为"线索"）

2) 这种加上了线索的二叉链表称为线索链表，相应的二叉树称为线索二叉树(Threaded BinaryTree)。根据线索性质 的不同，线索二叉树可分为前序线索二叉树、中序线索二叉树和后序线索二叉树三种

3) 一个结点的前一个结点，称为前驱结点

4) 一个结点的后一个结点，称为后继结点

### 1.3.3 线索二叉树应用案例

应用案例说明：将下面的二叉树，进行中序线索二叉树。中序遍历的数列为 {8, 3, 10, 1, 14, 6}

<img src = "/Users/chenghaozhang/Library/CloudStorage/OneDrive-Personal/MyBlog/chzhang1821.github.io/img/data_structure_imgs/树13.png" alt = "树13" style = "zoom: 50%"/>

<img src = "/Users/chenghaozhang/Library/CloudStorage/OneDrive-Personal/MyBlog/chzhang1821.github.io/img/data_structure_imgs/树14.png" alt = "树14" style = "zoom: 50%"/>

* 说明: 当线索化二叉树后，Node 节点的 属性 left 和 right ，有如下情况:

1) left 指向的是左子树，也可能是指向的前驱节点. 比如 ① 节点 left 指向的左子树, 而 ⑩ 节点的 left 指向的 就是前驱节点.

2) right 指向的是右子树，也可能是指向后继节点，比如 ① 节点 right 指向的是右子树，而⑩ 节点的 right 指向 的是后继节点.

代码实现：

```java
public class ThreadedBinaryTreeDemo {
    public static void main(String[] args) {
        HeroNode root = new HeroNode(1, "tom");
        HeroNode node2 = new HeroNode(3, "jack");
        HeroNode node3 = new HeroNode(6, "smith");
        HeroNode node4 = new HeroNode(8, "mary");
        HeroNode node5 = new HeroNode(10, "king");
        HeroNode node6 = new HeroNode(14, "dim");

        root.setLeft(node2);
        root.setRight(node3);
        node2.setLeft(node4);
        node2.setRight(node5);
        node3.setLeft(node6);

        ThreadedBinaryTree threadedBinaryTree = new ThreadedBinaryTree();
        threadedBinaryTree.setRoot(root);
        threadedBinaryTree.threadedNodes();

        HeroNode leftNode = node5.getLeft();
        System.out.println(leftNode);

        HeroNode rightNode = node5.getRight();
        System.out.println(rightNode);
    }
}

class ThreadedBinaryTree {
    private HeroNode root;
    // 为了线索化，需要创建要给指向当前节点的前驱节点的指针
    private HeroNode pre = null;

    public void setRoot(HeroNode root) {
        this.root = root;
    }

    public void threadedNodes() {
        this.threadedNodes(root);
    }

    // 编写二叉树进行中序线索化的方法
    /**
     *
     * @param node 当前需要线索化的节点
     */
    public void threadedNodes(HeroNode node) {
        if (node == null) {
            return;
        }

        // 先线索化左子树
        threadedNodes(node.getLeft());
        // 线索化当前节点
        // 处理当前节点的前驱节点
        if (node.getLeft() == null) {
            // 让当前节点的左指针指向前驱节点
            node.setLeft(pre);
            node.setLeftType(1);
        }

        // 处理后继结点
        if (pre != null && pre.getRight() == null) {
            // 让前驱节点的有指针指向当前节点
            pre.setRight(node);
            // 修改前驱节点的有指针类型
            pre.setRightType(1);
        }
        pre = node;

        // 线索化右子树
        threadedNodes((node.getRight()));
    }

    // 前序
    public void preOrder() {
        if (this.root != null) {
            this.root.preOrder();
        } else {
            System.out.println("当前二叉树为空，无法遍历");
        }
    }

    public void infixOrder() {
        if (this.root != null) {
            this.root.infixOrder();
        } else {
            System.out.println("当前二叉树为空，无法遍历");
        }
    }

    public void postOrder() {
        if (this.root != null) {
            this.root.postOrder();
        } else {
            System.out.println("当前二叉树为空，无法遍历");
        }
    }

    public HeroNode preOrderSearch(int no) {
        HeroNode res = null;
        if (this.root != null) {
            res = this.root.preOrderSearch(no);
        }
        return res;
    }

    public HeroNode infixOrderSearch(int no) {
        HeroNode res = null;
        if (this.root != null) {
            res = this.root.infixOrderSearch(no);
        }
        return res;
    }

    public HeroNode postOrderSearch(int no) {
        HeroNode res = null;
        if (this.root != null) {
            res = this.root.postOrderSearch(no);
        }
        return res;
    }

    public void delNode(int no) {
        if (root != null) {
            if (root.getNo() == no) {
                root = null;
            } else {
                root.delNode(no);
            }
        } else {
            System.out.println("这是一个空二叉树");
        }
    }
}
```



### 1.3.4 遍历线索化二叉树

1) 说明：对前面的中序线索化的二叉树， 进行遍历

2) 分析：因为线索化后，各个结点指向有变化，因此原来的遍历方式不能使用，这时需要使用新的方式遍历 线索化二叉树，各个节点可以通过线型方式遍历，因此无需使用递归方式，这样也提高了遍历的效率。 遍历的次 序应当和中序遍历保持一致。

3) 代码：

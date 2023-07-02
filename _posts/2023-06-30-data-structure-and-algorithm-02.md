---
layout:     post
title:      "数据结构与算法 02-稀疏数组和队列"
subtitle:   
date:       2023-06-30 16:00
author:     "Chenghao"
header-img: "img/post-bg-2015.jpg"
tags:
    - 数据结构与算法
    - Java
---

> 笔记摘自：尚硅谷 | B站视频链接：<https://www.bilibili.com/video/BV1E4411H73v/>

# 稀疏数组和队列

## 1. 稀疏数组（sparse array）

### 1.1 实际需求

编写的五子棋程序中，有存盘退出和续上盘的功能

<img src = "https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/data_structure_imgs/稀疏数组.png?raw=true" alt = "稀疏数组" style = "zoom: 50%"/>

分析问题：

因为该二维数组的很多值是默认值 0，因此记录了很多没有意义的数据 => 稀疏数组。



### 1.2 基本介绍

当一个数组中大部分元素为０，或者为同一个值的数组时，可以使用稀疏数组来保存该数组。

稀疏数组的处理方法是:

1) 记录数组一共有几行几列，有多少个不同的值

2) 把具有不同值的元素的行列及值记录在一个小规模的数组中，从而缩小程序的规模

<img src = "https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/data_structure_imgs/稀疏数组2.png?raw=true" alt = "稀疏数组2" style = "zoom: 50%"/>

### 1.3 应用实例

1) 使用稀疏数组，来保留类似前面的二维数组（棋盘、地图等等）

2) 把稀疏数组存盘，并且可以从新恢复原来的二维数组数

3) 整体思路分析

<img src = "https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/data_structure_imgs/稀疏数组3.png?raw=true" alt = "稀疏数组3" style = "zoom: 50%"/>

### 1.4 代码实现

```java
public class SparseArray01 {
    public static void main(String[] args) {
        // 创建一个原始的二维数组 11 * 11
        // 0: 表示没有棋子， 1 表示黑子 2 表示白子
        int chessArr1[][] = new int[11][11];
        chessArr1[1][2] = 1;
        chessArr1[2][3] = 2;
        chessArr1[4][5] = 2;

        // 输出原始的二维数组
        System.out.println("原始的二维数组：");
        for (int[] i : chessArr1) {
            for (int j : i) {
                System.out.print(j + " ");
            }
            System.out.println();
        }

        // 将二维数组转成稀疏数组
        // 1. 先遍历二维数组得到非 0 数据的个数
        int sum = 0;
        for (int[] i : chessArr1) {
            for (int j : i) {
                if (j != 0) {
                    sum++;
                }
            }
        }
        // 2. 创建对应的稀疏数组
        int[][] sparseArr = new int[sum + 1][3];
        // 给稀疏数组赋值
        sparseArr[0][0] = 11;
        sparseArr[0][1] = 11;
        sparseArr[0][2] = sum;
        // 遍历二维数组，将非 0 的值存放到 sparseArr 中
        int count = 1;
        for (int i = 0; i < 11; i++) {
            for (int j = 0; j < 11; j++) {
                if (chessArr1[i][j] != 0) {
                    sparseArr[count][0] = i;
                    sparseArr[count][1] = j;
                    sparseArr[count][2] = chessArr1[i][j];
                    count++;
                }
            }
        }
        // 输出稀疏数组的形式
        System.out.println("稀疏数组：");
        for (int[] i :sparseArr) {
            for (int j : i) {
                System.out.print(j + " ");
            }
            System.out.println();
        }

        //将稀疏数组 --》 恢复成 原始的二维数组
        /**
         * 1. 先读取稀疏数组的第一行，根据第一行的数据，创建原始的二维数组，比如上面的chessArr2 = int[11][11]
         * 2. 在读取稀疏数组后几行的数据，并赋给原始的二维数组 即可.
         * */
        // 1. 先读取稀疏数组的第一行，根据第一行的数据，创建原始的二维数组
        int[][] chessArr2 = new int[sparseArr[0][0]][sparseArr[0][1]];
        // 2. 在读取稀疏数组后几行的数据(从第二行开始)，并赋给 原始的二维数组 即可
        for (int i = 1; i < sparseArr.length; i++) {
            int x = sparseArr[i][0];
            int y = sparseArr[i][1];
            chessArr2[x][y] = sparseArr[i][2];
        }
        // 输出恢复后的二维数组
        System.out.println("恢复的二维数组：");
        for (int[] i : chessArr2) {
            for (int j : i) {
                System.out.print(j + " ");
            }
            System.out.println();
        }
    }
}
```

### 1.5 课后练习

要求：

1) 在前面的基础上，将稀疏数组保存到磁盘上，比如 map.data

2) 恢复原来的数组时，读取 map.data 进行恢复

```java
public class SparseArray01 {
    public static void main(String[] args) throws IOException {
				// ...........................

        // 保存数组
        String pathName = "/Users/chenghaozhang/Library/CloudStorage/OneDrive-Personal/Self-learning/CS/my_codes/data_structure/sparse_array/src/main/data/map.txt";
        File file = new File(pathName);
        if (!file.exists()) {
            file.createNewFile();
        }
        FileWriter fileWriter = new FileWriter(file);
        for (int i = 0; i < sparseArr.length; i++) {
            for (int j = 0; j < sparseArr[0].length; j++) {
                fileWriter.write(sparseArr[i][j] + ", ");
            }
            fileWriter.write("\n");
        }
        fileWriter.close();

        // 读取数组
        int[][] sparseArr2 = new int[4][3];
        int row = 0;
        BufferedReader br = new BufferedReader(new FileReader(file));
        String line;
        while ((line = br.readLine()) != null) {
            String[] temp = line.split(", ");
            for (int i = 0; i < temp.length; i++) {
                sparseArr2[row][i] = Integer.parseInt(temp[i]);
            }
            row++;
        }
        System.out.println("读取后的稀疏数组：");
        for (int[] i :sparseArr2) {
            for (int j : i) {
                System.out.print(j + " ");
            }
            System.out.println();
        }
        // 恢复原始数组
        int[][] chessArr = new int[sparseArr2[0][0]][sparseArr[0][1]];
        for (int i = 1; i < sparseArr2.length; i++) {
            chessArr[sparseArr2[i][0]][sparseArr2[i][1]] = sparseArr2[i][2];
        }
        System.out.println("读取后再恢复的棋盘数组：");
        for (int[] i : chessArr) {
            for (int j : i) {
                System.out.print(j + " ");
            }
            System.out.println();
        }

    }
}
```



## 2. 队列

### 2.1 队列的一个使用场景

银行排队案例

<img src = "https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/data_structure_imgs/队列1.png?raw=true" alt = "队列1" style = "zoom: 50%"/>

### 2.2 队列介绍

1) 队列是一个有序列表，可以用数组或是链表来实现。

2) 遵循先入先出的原则。即：先存入队列的数据，要先取出。后存入的要后取出

3) 示意图：(使用数组模拟队列示意图)


<img src = "https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/data_structure_imgs/队列2.png?raw=true" alt = "队列2" style = "zoom: 50%"/>

### 2.3 数组模拟队列思路

* 队列本身是有序列表，若使用数组的结构来存储队列的数据，则队列数组的声明如下图, 其中 maxSize 是该队 列的最大容量。 
* 因为队列的输出、输入是分别从前后端来处理，因此需要两个变量 front 及 rear 分别记录队列前后端的下标， front 会随着数据输出而改变，而 rear 则是随着数据输入而改变，如图所示:

<img src = "https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/data_structure_imgs/队列2.png?raw=true" alt = "队列2" style = "zoom: 50%"/>

当我们将数据存入队列时称为”addQueue”，addQueue 的处理需要有两个步骤：思路分析

1) 将尾指针往后移：rear + 1 , 当 front == rear [队列为空]

2) 若尾指针 rear 小于队列的最大下标 maxSize-1，则将数据存入 rear 所指的数组元素中，否则无法存入数据。 rear == maxSize - 1 [队列满]

代码实现：

```java
package com.chenghao;

import java.util.Scanner;

public class QueueExample01 {
    public static void main(String[] args) {
        ArrayQueue arrayQueue = new ArrayQueue(3);
        char key = ' '; // 接受用户输入
        Scanner sc = new Scanner(System.in);
        boolean loop = true;

        while (loop) {
            System.out.println("s(show): 显示队列");
            System.out.println("e(exit): 退出程序");
            System.out.println("a(add): 添加数据到队列");
            System.out.println("g(get): 从队列取出数据");
            System.out.println("h(head): 查看队列头的数据");
            key = sc.next().charAt(0); // 接受第一个字符
            switch (key) {
                case 's':
                    arrayQueue.showQueue();
                    break;
                case 'e':
                    sc.close();
                    loop = false;
                    break;
                case 'a':
                    try {
                        System.out.println("输入一个数");
                        int value = sc.nextInt();
                        queue.addQueue(value);
                    } catch (Exception e) {
                        System.out.println(e.getMessage());
                    }
                    break;
                case 'g':
                    try {
                        int res = queue.getQueue();
                        System.out.printf("取出的数据时 %d\n", res);
                    } catch (Exception e) {
                        System.out.println(e.getMessage());
                    }
                    break;
                case 'h':
                    try {
                        int res = queue.headQueue();
                        System.out.printf("队列头的数据是 %d\n", res);
                    } catch (Exception e) {
                        System.out.println(e.getMessage());
                    }
                    break;
                default:
                    break;
            }
        }
        System.out.println("退出程序");
    }
}

// 使用数组模拟队列 - 编写一个ArrayQueue类
class ArrayQueue {
    private int maxSize; // 表示数组的最大容量
    private int front; // 队列头
    private int rear; // 队列尾
    private int[] arr; // 该数组用于存放数据，模拟队列

    // 创建队列的构造器
    public ArrayQueue(int maxSize) {
        this.maxSize = maxSize;
        this.arr = new int[this.maxSize];
        this.front = -1; // 指向队列头部的前一个位置
        this.rear = -1; // 指向队列尾部的具体位置 (头部, 尾部]
    }

    // 判断队列是否满
    public boolean isFull() {
        return rear == maxSize - 1;
    }

    // 判断队列是否为空
    public boolean isEmpty() {
        return rear == front;
    }

    // 添加数据到队列
    public void addQueue(int num) {
        // 判断队列是否满
        if (isFull()) {
            throw new RuntimeException("队列满，无法加入数据");
        }
        rear++; // 让rear后移
        arr[rear] = num;
    }

    // 获取队列的数据
    public int getQueue() {
        // 判断队列是否空
        if (isEmpty()) {
            throw new RuntimeException("队列为空，不能取数据");
        }
        front++;
        return arr[front];
    }

    // 显示队列的所有数据
    public void showQueue() {
        if (isEmpty()) {
            System.out.println("队列空的，没有数据~~");
            return;
        }
        for (int i = 0; i < arr.length; i++) {
            System.out.printf("arr[%d] = %d\n", i, arr[i]);
        }
    }

    // 显示队列的头数据，注意不是取出数据
    public int headQueue() {
        if (isEmpty()) {
            throw new RuntimeException("队列空的，没有数据~~");
        }
        return arr[front + 1];
    }
}
```

> 问题分析并优化
>
> 1. 目前数组使用一次就不能用， 没有达到复用的效果
> 2) 将这个数组使用算法，改进成一个环形的队列 取模：%

### 2.4 数组模拟环形队列

对前面的数组模拟队列的优化，充分利用数组. 因此将数组看做是一个环形的。(通过取模的方式来实现即可)

分析说明：

1) 尾索引的下一个为头索引时表示队列满，即将队列容量空出一个作为约定,这个在做判断队列满的 时候需要注意 (rear + 1) % maxSize == front 满]

2) rear == front [空]

3) 分析示意图:

<img src = "https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/data_structure_imgs/队列3.png?raw=true" alt = "队列3" style = "zoom: 50%"/>

<img src = "https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/data_structure_imgs/队列4.png?raw=true" alt = "队列4" style = "zoom: 50%"/>

> 当队列位空时，条件就是front == rear，当队列满时，我们修改器条件，保留一个元素空间。也就是说，队列满时，数组中还有一个空闲单元。例如上图所示，我们就认为次队列已满。

代码实现

```java
package com.chenghao;

import java.util.Scanner;

public class CircleQueue {
    public static void main(String[] args) {
        Queue queue = new Queue(4);
        char key = ' '; // 接受用户输入
        Scanner sc = new Scanner(System.in);
        boolean loop = true;

        while (loop) {
            System.out.println("s(show): 显示队列");
            System.out.println("e(exit): 退出程序");
            System.out.println("a(add): 添加数据到队列");
            System.out.println("g(get): 从队列取出数据");
            System.out.println("h(head): 查看队列头的数据");
            key = sc.next().charAt(0); // 接受第一个字符
            switch (key) {
                case 's':
                    queue.showQueue();
                    break;
                case 'e':
                    sc.close();
                    loop = false;
                    break;
                case 'a':
                    try {
                        System.out.println("输入一个数");
                        int value = sc.nextInt();
                        queue.addQueue(value);
                    } catch (Exception e) {
                        System.out.println(e.getMessage());
                    }
                    break;
                case 'g':
                    try {
                        int res = queue.getQueue();
                        System.out.printf("取出的数据时 %d\n", res);
                    } catch (Exception e) {
                        System.out.println(e.getMessage());
                    }
                    break;
                case 'h':
                    try {
                        int res = queue.headQueue();
                        System.out.printf("队列头的数据是 %d\n", res);
                    } catch (Exception e) {
                        System.out.println(e.getMessage());
                    }
                    break;
                default:
                    break;
            }
        }
        System.out.println("退出程序");
    }
}


// 使用数组模拟队列 - 编写一个ArrayQueue类
class Queue {
    private int maxSize; // 表示数组的最大容量
    private int front; // 队列头
    private int rear; // 队列尾
    private int[] arr; // 该数组用于存放数据，模拟队列

    // 创建队列的构造器
    public Queue(int maxSize) {
        this.maxSize = maxSize;
        this.arr = new int[this.maxSize];
        this.front = 0; // 指向队列头部的具体位置
        this.rear = 0; // 指向队列尾部的后一位
    }

    // 判断队列是否满
    public boolean isFull() {
        return (rear + 1) % maxSize == front;
    }

    // 判断队列是否为空
    public boolean isEmpty() {
        return rear == front;
    }

    // 添加数据到队列
    public void addQueue(int num) {
        // 判断队列是否满
        if (isFull()) {
            throw new RuntimeException("队列满，无法加入数据");
        }
        arr[rear] = num;
        rear = (rear + 1) % maxSize; // 让rear后移
    }

    // 获取队列的数据
    public int getQueue() {
        // 判断队列是否空
        if (isEmpty()) {
            throw new RuntimeException("队列为空，不能取数据");
        }
        int value = arr[front];
        front = (front + 1) % maxSize;
        return value;
    }

    // 显示队列的所有数据
    public void showQueue() {
        if (isEmpty()) {
            System.out.println("队列空的，没有数据~~");
            return;
        }
        for (int i = front; i < front + size(); i++) {
            System.out.printf("arr[%d] = %d\n", i % maxSize, arr[i % maxSize]);
        }
    }

    public int size() {
        return (rear + maxSize - front) % maxSize;
    }

    // 显示队列的头数据，注意不是取出数据
    public int headQueue() {
        if (isEmpty()) {
            throw new RuntimeException("队列空的，没有数据~~");
        }
        return arr[front];
    }
}
```


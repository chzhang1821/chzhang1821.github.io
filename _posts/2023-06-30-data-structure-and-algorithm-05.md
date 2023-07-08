---
layout:     post
title:      "数据结构与算法 05-递归"
subtitle:   
date:       2023-06-30 16:00
author:     "Chenghao"
header-img: "img/post-bg-2015.jpg"
tags:
    - 数据结构与算法
    - Java
---

> 笔记摘自：尚硅谷 | B站视频链接：<https://www.bilibili.com/video/BV1E4411H73v/>

# 递归

## 1.1 递归应用场景

实际应用场景，迷宫问题(回溯)， 递归(Recursion)

<img src = "https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/data_structure_imgs/递归01.png?raw=true" alt = "递归01" style = "zoom: 50%" />

## 1.2 递归的概念

简单的说: 递归就是方法自己调用自己,每次调用时传入不同的变量.递归有助于编程者解决复杂的问题,同时 可以让代码变得简洁。



## 1.3 递归调用机制

1) 打印问题

```java
public static void test(int n) {
  	if (n > 2) {
      	test(n - 1);
    }
  	System.out.println("n = " + n);
}
```

2. 阶乘问题

   ```java
   public static int factorial(int n) {
       if (n == 1) {
           return 1;
       } else {
           return factorial(n - 1) * n;
       }
   }
   ```

3. 使用图解方式说明了递归的调用机制

<img src = "https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/data_structure_imgs/递归02.png?raw=true" alt = "递归02" style = "zoom: 50%" />

## 1.4 递归能解决什么样的问题

递归用于解决什么样的问题

1) 各种数学问题如: 8 皇后问题 , 汉诺塔, 阶乘问题, 迷宫问题, 球和篮子的问题(google 编程大赛)

2) 各种算法中也会使用到递归，比如快排，归并排序，二分查找，分治算法等.

3) 将用栈解决的问题-->第归代码比较简洁



## 1.5 递归需要遵守的重要规则

递归需要遵守的重要规则

1) 执行一个方法时，就创建一个新的受保护的独立空间(栈空间)

2) 方法的局部变量是独立的，不会相互影响, 比如 n 变量

3) 如果方法中使用的是引用类型变量(比如数组)，就会共享该引用类型的数据.

4) 递归必须向退出递归的条件逼近，否则就是无限递归,出现 StackOverflowError，死龟了:)

5) 当一个方法执行完毕，或者遇到 return，就会返回，遵守谁调用，就将结果返回给谁，同时当方法执行完毕或者返回时，该方法也就执行完毕



## 1.6 递归问题 - 迷宫

### 1.6.1 迷宫问题

<img src = "https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/data_structure_imgs/递归01.png?raw=true" alt = "递归01" style = "zoom: 50%" />

### 1.6.2 代码实现

```java
package com.chenghao;

public class Puzzel {
    public static void main(String[] args) {
        // 创建一个二维数组，模拟迷宫
        int[][] map = new int[8][7];
        // 使用1 表示墙
        // 上下全部置为1
        for (int i = 0; i < 7; i++) {
            map[0][i] = 1;
            map[7][i] = 1;
        }
        // 左右全部置为1
        for (int i = 0; i < 8; i++) {
            map[i][0] = 1;
            map[i][6] = 1;
        }
        // 设置挡板
        map[3][1] = 1;
        map[3][2] = 1;


        // 输出地图
        for (int i = 0; i < 8; i++) {
            for (int j = 0; j < 7; j++) {
                System.out.print(map[i][j] + " ");
            }
            System.out.println();
        }

        System.out.println("===================");

        // 使用递归回溯给小球找路
        setWay2(map, 1, 1);
        // 输出新的地图
        for (int i = 0; i < 8; i++) {
            for (int j = 0; j < 7; j++) {
                System.out.print(map[i][j] + " ");
            }
            System.out.println();
        }

    }


    // 说明
    // 1. map 表示地图
    // 2. i, j 表示从地图的哪个位置开始出发
    // 3. 如果小球能到 map[6][5] 位置，则说明通路找到
    // 4. 约定：当 map[i][j] 为0表示该店没有走过 当为1表示墙，2表示通路可以走，3表示该点已经走过，但是走不通
    // 5. 在走迷宫时，确定一个策略（方法）下->右->上->左，如果该点走不通再回溯
    /**
     * 使用递归回溯来给小球找路
     * @param map 表示地图
     * @param i 从哪个位置开始出发
     * @param j
     * @return 如果找到通路，返回true，反之返回false
     */
    public static boolean setWay(int[][] map, int i, int j) {
        if (map[6][5] == 2) { // 该点已经找到
            return true;
        } else {
            if (map[i][j] == 0) { // 如果该点还没有走过
                // 按照策略 下->右->上->左
                map[i][j] = 2; // 假定该点可以走通
                if (setWay(map, i + 1, j)) { // 向下走
                    return true;
                } else if (setWay(map, i, j + 1)) { // 向右走
                    return true;
                } else if (setWay(map, i - 1, j)) { // 向上走
                    return true;
                } else if (setWay(map, i, j - 1)) { // 向左走
                    return true;
                } else {
                    map[i][j] = 3;
                    return false;
                }
            } else { // map[i][j] == 1 | 2 | 3
                return false;
            }
        }
    }

    // 修改策略：上->右->下->左
    public static boolean setWay2(int[][] map, int i, int j) {
        if (map[6][5] == 2) { // 该点已经找到
            return true;
        } else {
            if (map[i][j] == 0) { // 如果该点还没有走过
                // 按照策略 下->右->上->左
                map[i][j] = 2; // 假定该点可以走通
                if (setWay2(map, i - 1, j)) { // 向上走
                    return true;
                } else if (setWay2(map, i, j + 1)) { // 向右走
                    return true;
                } else if (setWay2(map, i + 1, j)) { // 向下走
                    return true;
                } else if (setWay2(map, i, j - 1)) { // 向左走
                    return true;
                } else {
                    map[i][j] = 3;
                    return false;
                }
            } else { // map[i][j] == 1 | 2 | 3
                return false;
            }
        }
    }
}

```



### 1.6.3 对迷宫问题的讨论

1) 小球得到的路径，和程序员设置的找路策略有关即：找路的上下左右的顺序相关

2) 再得到小球路径时，可以先使用(下右上左)，再改成(上右下左)，看看路径是不是有变化

3) 测试回溯现象

4) 思考: 如何求出最短路径? 思路 -> 代码实现.



## 1.7 递归-八皇后问题(回溯算法)

## 1.7.1 八皇后问题介绍

八皇后问题，是一个古老而著名的问题，是回溯算法的典型案例。该问题是国际西洋棋棋手马克斯·贝瑟尔于 1848 年提出：在 8×8 格的国际象棋上摆放八个皇后，使其不能互相攻击，即：任意两个皇后都不能处于同一行、 同一列或同一斜线上，问有多少种摆法(92)。

<img src = "https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/data_structure_imgs/递归03.png?raw=true" alt = "递归03" style = "zoom: 50%" />

### 1.7.2 八皇后问题算法思路分析

1) 第一个皇后先放第一行第一列

2) 第二个皇后放在第二行第一列、然后判断是否 OK， 如果不 OK，继续放在第二列、第三列、依次把所有列都 放完，找到一个合适

3) 继续第三个皇后，还是第一列、第二列……直到第 8 个皇后也能放在一个不冲突的位置，算是找到了一个正确 解

4) 当得到一个正确解时，在栈回退到上一个栈时，就会开始回溯，即将第一个皇后，放到第一列的所有正确解， 全部得到.

5) 然后回头继续第一个皇后放第二列，后面继续循环执行 1,2,3,4 的步骤

6) 示意图:

<img src = "https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/data_structure_imgs/递归04.png?raw=true" alt = "递归04" style = "zoom: 50%" />

> 说明： 理论上应该创建一个二维数组来表示棋盘，但是实际上可以通过算法，用一个一维数组即可解决问题. `arr[8] = {0 , 4, 7, 5, 2, 6, 1, 3}` //对应 arr 下标 表示第几行，即第几个皇后，`arr[i] = val` ，`val` 表示第 `i+1` 个皇后，放在第` i+1` 行的第 `val+1` 列`



### 1.7.3 八皇后问题算法代码实现

```java
package com.chenghao;

public class Queen8 {
    // 定义一个max表示共有多少个皇后
    int max = 8;
    // 定义一个数组array，保存皇后放置位置的结果，例如 arr[8] = {0 , 4, 7, 5, 2, 6, 1, 3}
    int[] array = new int[max];
    static int count = 0;

    public static void main(String[] args) {
        Queen8 queen8 = new Queen8();
        queen8.check(0);
        System.out.println("一共有 " + count + " 种解法");

    }
    
    /**
     * 编写一个方法，放置第n个皇后
     * 特别注意: check 是每一次递归都包含一个for循环，因此会有回溯
     * @param n 第n个皇后
     */
    private void check(int n) {
        if (n == max) {
            print();
            return;
        }

        // 依次将皇后放入不同的列，并判断是否冲突
        for (int i = 0; i < max; i++) {
            // 先把当前这个皇后 n 放到该行的第 1 列
            array[n] = i;
            // 判断当放置第 n 个皇后到第 i 列时，是否冲突
            if (judge(n)) {
                // 接着放入n+1个皇后，即开始递归
                check(n + 1);
            }
            // 如果冲突，就继续执行循环，将皇后放到本行的下一列
        }
    }

    /**
     * 查看当我们放置第n个皇后，就去检测该皇后是否和前面已经摆放的皇后是否冲突
     * @param n 表示第n个皇后
     * @return
     */
    private boolean judge(int n) {
        for (int i = 0; i < n; i++) {
            // 说明：
            // 1. array[i] == array[n]  表示判断 第n个皇后是否和前面的i个皇后在同一列
            // 2. Math.abs(n - i) == Math.abs(array[n] - array[i])  表示判断 第n个皇后是否和前面的i个皇后在同一斜线
            // 3. 不用判断是否在同一行，因为 n 每次都在递增
            if (array[i] == array[n] || Math.abs(n - i) == Math.abs(array[n] - array[i])) {
                return false;
            }
        }
        return true;
    }

    // 写一个方法，可以将皇后摆放的位置输出
    private void print() {
        for (int i = 0; i < array.length; i++) {
            System.out.print(array[i] + " ");
        }
        System.out.println();
        count++;
    }
}
```


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

# 稀疏数组和队列



## 1. 稀疏数组（sparse array）

### 1.1 实际需求

编写的五子棋程序中，有存盘退出和续上盘的功能

<img src = "/Users/chenghaozhang/Library/CloudStorage/OneDrive-Personal/MyBlog/chzhang1821.github.io/img/data_structure_imgs/稀疏数组.png" alt = "稀疏数组" style = "zoom: 50%"/>

分析问题：

因为该二维数组的很多值是默认值 0，因此记录了很多没有意义的数据 => 稀疏数组。



### 1.2 基本介绍

当一个数组中大部分元素为０，或者为同一个值的数组时，可以使用稀疏数组来保存该数组。

稀疏数组的处理方法是:

1) 记录数组一共有几行几列，有多少个不同的值

2) 把具有不同值的元素的行列及值记录在一个小规模的数组中，从而缩小程序的规模

<img src = "/Users/chenghaozhang/Library/CloudStorage/OneDrive-Personal/MyBlog/chzhang1821.github.io/img/data_structure_imgs/稀疏数组2.png" alt = "稀疏数组2" style = "zoom: 50%"/>

### 1.3 应用实例

1) 使用稀疏数组，来保留类似前面的二维数组（棋盘、地图等等）

2) 把稀疏数组存盘，并且可以从新恢复原来的二维数组数

3) 整体思路分析

<img src = "/Users/chenghaozhang/Library/CloudStorage/OneDrive-Personal/MyBlog/chzhang1821.github.io/img/data_structure_imgs/稀疏数组3.png" alt = "稀疏数组3" style = "zoom: 50%"/>

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

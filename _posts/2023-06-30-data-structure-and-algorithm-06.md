---
layout:     post
title:      "数据结构与算法 05-排序算法"
subtitle:   
date:       2023-07-07 16:00
author:     "Chenghao"
header-img: "img/post-bg-2015.jpg"
tags:
    - 数据结构与算法
    - Java
    - 时间复杂度
---

> 笔记摘自：尚硅谷 | B站视频链接：<https://www.bilibili.com/video/BV1E4411H73v/>



# 排序算法

## 1.1 排序算法的介绍

排序也称排序算法(Sort Algorithm)，排序是将一组数据，依指定的顺序进行排列的过程。

## 1.2 排序的分类：

1) 内部排序:

指将需要处理的所有数据都加载到内部存储器(内存)中进行排序。

2) 外部排序法：

数据量过大，无法全部加载到内存中，需要借助外部存储(文件等)进行排序。

3) 常见的排序算法分类(见右图):

<img src = "/Users/chenghaozhang/Library/CloudStorage/OneDrive-Personal/MyBlog/chzhang1821.github.io/img/data_structure_imgs/排序01.png" alt = "排序01" style = "zoom : 50%"/>

## 1.3 算法的时间复杂度

### 1.3.1 度量一个程序(算法)执行时间的两种方法

1) 事后统计的方法

这种方法可行, 但是有两个问题：一是要想对设计的算法的运行性能进行评测，需要实际运行该程序；二是所 得时间的统计量依赖于计算机的硬件、软件等环境因素，这种方式，要在同一台计算机的相同状态下运行，才能比较那个算法速度更快。

2) 事前估算的方法 通过分析某个算法的时间复杂度来判断哪个算法更优.

### 1.3.2 时间频度

* 基本介绍 

  时间频度：一个算法花费的时间与算法中语句的执行次数成正比例，哪个算法中语句执行次数多，它花费时间就多。一个算法中的语句执行次数称为语句频度或时间频度。记为 $T(n)$。[举例说明] 

* 举例说明-基本案例 

  比如计算 1-100 所有数字之和, 我们设计两种算法：

<img src = "/Users/chenghaozhang/Library/CloudStorage/OneDrive-Personal/MyBlog/chzhang1821.github.io/img/data_structure_imgs/排序02.png" alt = "排序02" style = "zoom : 50%"/>

* 举例说明-忽略常数项

<img src = "/Users/chenghaozhang/Library/CloudStorage/OneDrive-Personal/MyBlog/chzhang1821.github.io/img/data_structure_imgs/排序03.png" alt = "排序03" style = "zoom : 50%"/>

结论:

1) 2n+20 和 2n 随着 n 变大，执行曲线无限接近, 20 可以忽略

2) 3n+10 和 3n 随着 n 变大，执行曲线无限接近, 10 可以忽略



* 举例说明-忽略低次项

<img src = "/Users/chenghaozhang/Library/CloudStorage/OneDrive-Personal/MyBlog/chzhang1821.github.io/img/data_structure_imgs/排序04.png" alt = "排序04" style = "zoom : 50%"/>

结论:

1) $2n^2+3n+10$ 和 $2n^2$ 随着 n 变大, 执行曲线无限接近, 可以忽略 $3n+10$

2) $n^2+5n+20$ 和 $n^2$ 随着 n 变大,执行曲线无限接近, 可以忽略 $5n+20$



* 举例说明-忽略系数

<img src = "/Users/chenghaozhang/Library/CloudStorage/OneDrive-Personal/MyBlog/chzhang1821.github.io/img/data_structure_imgs/排序05.png" alt = "排序05" style = "zoom : 50%"/>

结论:

1) 随着 n 值变大，$5n^2+7n$ 和 $3n^2 + 2n$ ，执行曲线重合, 说明

2) 而 $n^3+5n$ 和 $6n^3+4n$ ，执行曲线分离，说明多少次方式关键



### 1.3.3 时间复杂度

1) 一般情况下，算法中的基本操作语句的重复执行次数是问题规模 n 的某个函数，用 $T(n)$表示，若有某个辅 助函数$ f(n)$，使得当 n 趋近于无穷大时，$T(n) / f(n)$ 的极限值为不等于零的常数，则称$ f(n)$是$ T(n)$的同数量级函数。 记作$ T(n)=Ｏ(f(n))$，称$Ｏ( f(n) )$ 为算法的渐进时间复杂度，简称时间复杂度。

2) $T(n)$ 不同，但时间复杂度可能相同。 如：$T(n)=n²+7n+6$ 与 $T(n)=3n²+2n+2$ 它们的 $T(n)$ 不同，但时间复杂 度相同，都为$ O(n²)$。

3. 计算时间复杂度的方法：

   * 用常数 1 代替运行时间中的所有加法常数 $T(n)=n²+7n+6 => T(n)=n²+7n+1 $

   * 修改后的运行次数函数中，只保留最高阶项 $T(n)=n²+7n+1 => T(n) = n²$ 

   * 去除最高阶项的系数 $T(n) = n² => T(n) = n² => O(n²)$

### 1.3.4 常见的时间复杂度

1. 常数阶 $O(1)$

   <img src = "/Users/chenghaozhang/Library/CloudStorage/OneDrive-Personal/MyBlog/chzhang1821.github.io/img/data_structure_imgs/排序07.png" alt = "排序07" style = "zoom : 50%"/>

2. 对数阶 $O(log_2n)$

   <img src = "/Users/chenghaozhang/Library/CloudStorage/OneDrive-Personal/MyBlog/chzhang1821.github.io/img/data_structure_imgs/排序08.png" alt = "排序08" style = "zoom : 50%"/>

3. 线性阶$ O(n)$

   <img src = "/Users/chenghaozhang/Library/CloudStorage/OneDrive-Personal/MyBlog/chzhang1821.github.io/img/data_structure_imgs/排序09.png" alt = "排序09" style = "zoom : 50%"/>

4. 线性对数阶 $O(nlog_2n)$

   <img src = "/Users/chenghaozhang/Library/CloudStorage/OneDrive-Personal/MyBlog/chzhang1821.github.io/img/data_structure_imgs/排序10.png" alt = "排序10" style = "zoom : 50%"/>

5. 平方阶 $O(n^2)$

   <img src = "/Users/chenghaozhang/Library/CloudStorage/OneDrive-Personal/MyBlog/chzhang1821.github.io/img/data_structure_imgs/排序11.png" alt = "排序11" style = "zoom : 50%"/>

6. 立方阶 $O(n^3)$

7. k 次方阶 $O(n^k)$

8. 指数阶 $O(2^n)$

常见的时间复杂度对应的图:

<img src = "/Users/chenghaozhang/Library/CloudStorage/OneDrive-Personal/MyBlog/chzhang1821.github.io/img/data_structure_imgs/排序06.png" alt = "排序06" style = "zoom : 50%"/>

说明：

1) 常见的算法时间复杂度由小到大依次为：$Ο(1)＜Ο(log_2n)＜Ο(n)＜Ο(nlog_2n)＜Ο(n^2)＜Ο(n^3)＜ Ο(n^k) ＜ Ο(2^n) $，随着问题规模 n 的不断增大，上述时间复杂度不断增大，算法的执行效率越低

2) 从图中可见，我们应该尽可能避免使用指数阶的算法



### 1.3.5 平均时间复杂度和最坏时间复杂度

1) 平均时间复杂度是指所有可能的输入实例均以等概率出现的情况下，该算法的运行时间。

2) 最坏情况下的时间复杂度称最坏时间复杂度。一般讨论的时间复杂度均是最坏情况下的时间复杂度。这样做的 原因是：最坏情况下的时间复杂度是算法在任何输入实例上运行时间的界限，这就保证了算法的运行时间不会 比最坏情况更长。

3) 平均时间复杂度和最坏时间复杂度是否一致，和算法有关(如图:)。

<img src = "/Users/chenghaozhang/Library/CloudStorage/OneDrive-Personal/MyBlog/chzhang1821.github.io/img/data_structure_imgs/排序12.png" alt = "排序12" style = "zoom : 50%"/>



## 1.4 算法的空间复杂度简介

### 1.4.1 基本介绍

1) 类似于时间复杂度的讨论，一个算法的空间复杂度(Space Complexity)定义为该算法所耗费的存储空间，它也是 问题规模 n 的函数。

2) 空间复杂度(Space Complexity)是对一个算法在运行过程中临时占用存储空间大小的量度。有的算法需要占用的 临时工作单元数与解决问题的规模 n 有关，它随着 n 的增大而增大，当 n 较大时，将占用较多的存储单元，例 如快速排序和归并排序算法, 基数排序就属于这种情况

3) 在做算法分析时，主要讨论的是时间复杂度。从用户使用体验上看，更看重的程序执行的速度。一些缓存产品 (redis, memcache)和算法(基数排序)本质就是用空间换时间.





## 1.5 冒泡排序

### 1.5.1 基本介绍

冒泡排序（Bubble Sorting）的基本思想是：通过对待排序序列从前向后（从下标较小的元素开始），依次比较 相邻元素的值，若发现逆序则交换，使值较大的元素逐渐从前移向后部，就象水底下的气泡一样逐渐向上冒。

优化： 

因为排序的过程中，各元素不断接近自己的位置，如果一趟比较下来没有进行过交换，就说明序列有序，因此要在 排序过程中设置一个标志 flag 判断元素是否进行过交换。从而减少不必要的比较。

### 1.5.2 图示

<img src = "/Users/chenghaozhang/Library/CloudStorage/OneDrive-Personal/MyBlog/chzhang1821.github.io/img/data_structure_imgs/排序13.png" alt = "排序13" style = "zoom : 50%"/>

### 1.5.3 代码实现

```java
public class BubbleSort {
    public static void main(String[] args) {
        int[] arr = {3, 9, -1, 10, -2};
        int[] sortedArr = bubbleSort(arr);
        System.out.println(Arrays.toString(sortedArr));
    }

    public static int[] bubbleSort(int[] arr) {
        boolean flag = false; // 标识变量，表示是否进行过交换
        for (int i = 0; i < arr.length - 1; i++) {
            for (int j = 0; j < arr.length - 1 - i; j++) {
                if (arr[j] > arr[j + 1]) {
                    flag = true;
                    int temp = arr[j];
                    arr[j] = arr[j + 1];
                    arr[j + 1] = temp;
                }
            }
            if (flag == false) { //  在一趟排序中，一次都没有交换过
                break;
            } else {
                flag = false; // 重置flag进行下次判断
            }
        }
        return arr;
    }
}
```





## 1.6 选择排序

### 1.6.1 基本介绍

选择式排序也属于内部排序法，是从欲排序的数据中，按指定的规则选出某一元素，再依规定交换位置后达到 排序的目的。

### 1.6.2 选择排序思想:

选择排序（select sorting）也是一种简单的排序方法。它的基本思想是：第一次从 `arr[0]~arr[n-1]`中选取最小值， 与 `arr[0]`交换，第二次从 `arr[1]~arr[n-1]`中选取最小值，与 `arr[1]`交换，第三次从 `arr[2]~arr[n-1]`中选取最小值，与 `arr[2] `交换，…，第 i 次从 `arr[i-1]~arr[n-1]`中选取最小值，与` arr[i-1]`交换，…, 第 n-1 次从 `arr[n-2]~arr[n-1]`中选取最小值， 与 `arr[n-2]`交换，总共通过 n-1 次，得到一个按排序码从小到大排列的有序序列。

### 1.6.3 选择排序思路分析图:

<img src = "/Users/chenghaozhang/Library/CloudStorage/OneDrive-Personal/MyBlog/chzhang1821.github.io/img/data_structure_imgs/排序14.png" alt = "排序14" style = "zoom : 50%"/>

<img src = "/Users/chenghaozhang/Library/CloudStorage/OneDrive-Personal/MyBlog/chzhang1821.github.io/img/data_structure_imgs/排序15.png" alt = "排序15" style = "zoom : 50%"/>

### 1.6.4 代码实现

```java
public class SelectSort {
    public static void main(String[] args) {
        int[] arr = {10, -2, 5, 3, 20};
        int[] sortedArr = selectSort(arr);
        System.out.println(Arrays.toString(sortedArr));
    }

    public static int[] selectSort(int[] arr) {
        for (int i = 0; i < arr.length - 1; i++) {
            int min = arr[i];
            int index = i;
            for (int j = i + 1; j < arr.length; j++) {
                if (min > arr[j]) {
                    min = arr[j];
                    index = j;
                }
            }
            if (index != i) {
                arr[index] = arr[i];
                arr[i] = min;
            }
        }
        return arr;
    }
}
```



## 1.7 插入排序

### 1.7.1 插入排序法介绍:

插入式排序属于内部排序法，是对于欲排序的元素以插入的方式找寻该元素的适当位置，以达到排序的目的。

### 1.7.2 插入排序法思想:

插入排序（Insertion Sorting）的基本思想是：把 n 个待排序的元素看成为一个有序表和一个无序表，开始时有 序表中只包含一个元素，无序表中包含有 n-1 个元素，排序过程中每次从无序表中取出第一个元素，把它的排 序码依次与有序表元素的排序码进行比较，将它插入到有序表中的适当位置，使之成为新的有序表。

### 1.7.3 插入排序思路图:

<img src = "/Users/chenghaozhang/Library/CloudStorage/OneDrive-Personal/MyBlog/chzhang1821.github.io/img/data_structure_imgs/排序16.png" alt = "排序16" style = "zoom : 50%"/>

### 1.7.4 代码实现


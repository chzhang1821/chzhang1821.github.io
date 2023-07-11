---
layout:     post
title:      "数据结构与算法 06-排序算法"
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

<img src = "https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/data_structure_imgs/排序01.png?raw=true?raw=true" alt = "排序01" style = "zoom : 50%"/>

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

<img src = "https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/data_structure_imgs/排序02.png?raw=true" alt = "排序02" style = "zoom : 50%"/>

* 举例说明-忽略常数项

<img src = "https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/data_structure_imgs/排序03.png?raw=true" alt = "排序03" style = "zoom : 50%"/>

结论:

1) 2n+20 和 2n 随着 n 变大，执行曲线无限接近, 20 可以忽略

2) 3n+10 和 3n 随着 n 变大，执行曲线无限接近, 10 可以忽略



* 举例说明-忽略低次项

<img src = "https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/data_structure_imgs/排序04.png?raw=true" alt = "排序04" style = "zoom : 50%"/>

结论:

1) $2n^2+3n+10$ 和 $2n^2$ 随着 n 变大, 执行曲线无限接近, 可以忽略 $3n+10$

2) $n^2+5n+20$ 和 $n^2$ 随着 n 变大,执行曲线无限接近, 可以忽略 $5n+20$



* 举例说明-忽略系数

<img src = "https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/data_structure_imgs/排序05.png?raw=true" alt = "排序05" style = "zoom : 50%"/>

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

   <img src = "https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/data_structure_imgs/排序07.png?raw=true" alt = "排序07" style = "zoom : 50%"/>

2. 对数阶 $O(log_2n)$

   <img src = "https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/data_structure_imgs/排序08.png?raw=true" alt = "排序08" style = "zoom : 50%"/>

3. 线性阶$ O(n)$

   <img src = "https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/data_structure_imgs/排序09.png?raw=true" alt = "排序09" style = "zoom : 50%"/>

4. 线性对数阶 $O(nlog_2n)$

   <img src = "https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/data_structure_imgs/排序10.png?raw=true" alt = "排序10" style = "zoom : 50%"/>

5. 平方阶 $O(n^2)$

   <img src = "https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/data_structure_imgs/排序11.png?raw=true" alt = "排序11" style = "zoom : 50%"/>

6. 立方阶 $O(n^3)$

7. k 次方阶 $O(n^k)$

8. 指数阶 $O(2^n)$

常见的时间复杂度对应的图:

<img src = "https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/data_structure_imgs/排序06.png?raw=true" alt = "排序06" style = "zoom : 50%"/>

说明：

1) 常见的算法时间复杂度由小到大依次为：$Ο(1)＜Ο(log_2n)＜Ο(n)＜Ο(nlog_2n)＜Ο(n^2)＜Ο(n^3)＜ Ο(n^k) ＜ Ο(2^n) $，随着问题规模 n 的不断增大，上述时间复杂度不断增大，算法的执行效率越低

2) 从图中可见，我们应该尽可能避免使用指数阶的算法



### 1.3.5 平均时间复杂度和最坏时间复杂度

1) 平均时间复杂度是指所有可能的输入实例均以等概率出现的情况下，该算法的运行时间。

2) 最坏情况下的时间复杂度称最坏时间复杂度。一般讨论的时间复杂度均是最坏情况下的时间复杂度。这样做的 原因是：最坏情况下的时间复杂度是算法在任何输入实例上运行时间的界限，这就保证了算法的运行时间不会 比最坏情况更长。

3) 平均时间复杂度和最坏时间复杂度是否一致，和算法有关(如图:)。

<img src = "https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/data_structure_imgs/排序12.png?raw=true" alt = "排序12" style = "zoom : 50%"/>



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

<img src = "https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/data_structure_imgs/排序13.png?raw=true" alt = "排序13" style = "zoom : 50%"/>

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

<img src = "https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/data_structure_imgs/排序14.png?raw=true" alt = "排序14" style = "zoom : 50%"/>

<img src = "https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/data_structure_imgs/排序15.png?raw=true" alt = "排序15" style = "zoom : 50%"/>

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

<img src = "https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/data_structure_imgs/排序16.png?raw=true" alt = "排序16" style = "zoom : 50%"/>

### 1.7.4 代码实现

```java
public class InsertSort {
    public static void main(String[] args) {
        int[] arr = {101, 34, 119, 1, -1, 89};
        insertSort(arr);
    }

    public static void insertSort(int[] arr) {
        for (int i = 1; i < arr.length; i++) {
            // 定义待插入的数
            int insertVal = arr[i];
            int insertIndex = i - 1; // 即arr[1]的前面这个数的下标

            // 给insertVal找到插入的位置
            // 说明：
            // 1. insertIndex >= 0 保证在给insertVal 找到插入位置不越界
            // 2. insertVal < arr[insertIndex] 待插入的数没有找到插入位置
            while (insertIndex >= 0 && insertVal < arr[insertIndex]) {
                arr[insertIndex + 1] = arr[insertIndex];
                insertIndex--;
            }
            // 退出while循环时，说明插入的位置找到，insertIndex + 1
            arr[insertIndex + 1] = insertVal;

            System.out.println("第" + i + "轮数组");
            System.out.println(Arrays.toString(arr));
        }
    }
}

```



## 1.8 希尔排序

### 1.8.1 简单插入排序存在的问题

我们看简单的插入排序可能存在的问题.

数组 arr = {2,3,4,5,6,1} 这时需要插入的数 1(最小), 这样的过程是： 

```
{2,3,4,5,6,6} 
{2,3,4,5,5,6} 
{2,3,4,4,5,6}
{2,3,3,4,5,6} 
{2,2,3,4,5,6} 
{1,2,3,4,5,6}
```

结论: 当需要插入的数是较小的数时，后移的次数明显增多，对效率有影响.



### 1.8.2 希尔排序法介绍

希尔排序是希尔（Donald Shell）于 1959 年提出的一种排序算法。希尔排序也是一种插入排序，它是简单插入排序经过改进之后的一个更高效的版本，也称为缩小增量排序。

### 1.8.3 希尔排序法基本思想

希尔排序是把记录按下标的一定增量分组，对每组使用直接插入排序算法排序；随着增量逐渐减少，每组包含的关键词越来越多，当增量减至 1 时，整个文件恰被分成一组，算法便终止

### 1.8.4 希尔排序法的示意图

<img src = "https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/data_structure_imgs/希尔01.png?raw=true" alt = "希尔01" style = "zoom : 50%"/>

<img src = "https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/data_structure_imgs/希尔02.png?raw=true" alt = "希尔02" style = "zoom : 50%"/>



### 1.8.5 希尔排序法应用实例:

有一群小牛, 考试成绩分别是 {8,9,1,7,2,3,5,4,6,0} 请从小到大排序. 请分别使用

1) 希尔排序时， 对有序序列在插入时采用交换法, 并测试排序速度.

2) 希尔排序时， 对有序序列在插入时采用移动法, 并测试排序速度

3) 代码实现

```java
public class ShellSort {
    public static void main(String[] args) {
        int[] arr = {8, 9, 1, 7, 2, 3, 5, 4, 6, 0};
        shellSort2(arr);
    }

    public static void shellSort(int[] arr) {
        int step = arr.length / 2;
        int temp = 0;
        while (step > 0) {
            for (int i = step; i < arr.length; i++) {
                for (int j = i - step; j >= 0; j -= step) {
                    if (arr[j] > arr[j + step]) {
                        temp = arr[j];
                        arr[j] = arr[j + step];
                        arr[j + step] = temp;
                    }
                }
            }
            step /= 2;
        }
        System.out.println(Arrays.toString(arr));
    }

    // 对交换式希尔排序进行优化 -> 移位法
    public static void shellSort2(int[] arr) {
        int step = arr.length / 2;
        while (step > 0) {
            for (int i = step; i < arr.length; i++) {
                int j = i;
                int temp = arr[j];
                if (arr[j] < arr[j - step]) {
                    while (j - step >= 0 && temp < arr[j - step]) {
                        arr[j] = arr[j - step];
                        j -= step;
                    }
                    arr[j] = temp;
                }
            }
            step /= 2;
        }
        System.out.println(Arrays.toString(arr));
    }
}
```









## 1.9 快速排序

### 1.9.1 快速排序法介绍:

快速排序（Quicksort）是对冒泡排序的一种改进。基本思想是：通过一趟排序将要排序的数据分割成独立的两部分，其中一部分的所有数据都比另外一部分的所有数据都要小，然后再按此方法对这两部分数据分别进行快速排 序，整个排序过程可以递归进行，以此达到整个数据变成有序序列

### 1.9.2 快速排序法示意图:

<img src = "https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/data_structure_imgs/快排01.png?raw=true" alt = "快排01" style = "zoom : 50%"/>

### 1.9.3 快速排序法应用实例:

要求: 对 `{-9,78,0,23,-567,70}`进行从小到大的排序，要求使用快速排序法。【测试 8w 和 800w】 说明[验证分析]:

1) 如果取消左右递归，结果是 `{-9, -567, 0, 23, 78, 70}`

2) 如果取消右递归,结果是 `{-567, -9, 0, 23, 78, 70}`

3) 如果取消左递归,结果是 `{-9, -567, 0, 23, 70, 78}`

4) 代码实现

```java
public class QuickSort {
    public static void main(String[] args) {
        int[] arr = {-9, 78, 0, 23, -567, 70};
        quickSort(arr, 0, arr.length - 1);
        System.out.println(Arrays.toString(arr));
    }

    public static void quickSort(int[] arr, int left, int right) {
        if (left < right) {
            int pivot = partition(arr, left, right);
            quickSort(arr, left, pivot - 1);
            quickSort(arr, pivot + 1, right);
        }
    }

    public static int partition(int[] arr, int left, int right) {
        int pivotKey = arr[left];
        while (left < right) {
            while (left < right && arr[right] >= pivotKey) {
                right--;
            }
            swap(arr, left, right);
            while (left < right && arr[left] <= pivotKey) {
                left++;
            }
            swap(arr, left, right);
        }
        return left;
    }

    public static void swap(int[] arr, int left, int right) {
        int temp = arr[left];
        arr[left] = arr[right];
        arr[right] = temp;
    }
}
```



## 1.10 归并排序

### 1.10.1 归并排序介绍:

归并排序（MERGE-SORT）是利用归并的思想实现的排序方法，该算法采用经典的分治（divide-and-conquer） 策略（分治法将问题分(divide)成一些小的问题然后递归求解，而治(conquer)的阶段则将分的阶段得到的各答案"修 补"在一起，即分而治之)。

### 1.10.2 归并排序思想示意图 1-基本思想:

<img src = "https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/data_structure_imgs/分治01.png?raw=true" alt = "分治01" style = "zoom : 50%"/>

### 1.10.3 归并排序思想示意图 2-合并相邻有序子序列:

再来看看治阶段，我们需要将两个已经有序的子序列合并成一个有序序列，比如上图中的最后一次合并，要将 [4,5,7,8]和[1,2,3,6]两个已经有序的子序列，合并为最终序列[1,2,3,4,5,6,7,8]，来看下实现步骤

<img src = "https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/data_structure_imgs/分治02.png?raw=true" alt = "分治02" style = "zoom : 50%"/>

### 1.10.4 归并排序的应用实例:

给你一个数组, val arr = Array(8, 4, 5, 7, 1, 3, 6, 2 ), 请使用归并排序完成排序。

```java
public class MergeSort {
    public static void main(String[] args) {
        int[] arr = {8, 4, 5, 7, 1, 3, 6, 2};
        int[] temp = new int[arr.length];
        mergeSort(arr, 0, arr.length - 1, temp);
        System.out.println(Arrays.toString(arr));
    }

    // 分 + 合
    public static void mergeSort(int[] arr, int left, int right, int[] temp) {
        if (left < right) {
            int mid = (left + right) / 2;
            mergeSort(arr, left, mid, temp);
            mergeSort(arr, mid + 1, right, temp);
            merge(arr, left, mid, right, temp);
        }
    }
    
    /**
     *
     * @param arr
     * @param left 左边有序序列的初始索引
     * @param mid
     * @param right
     * @param temp 中转数组
     */
    public static void merge(int[] arr, int left, int mid, int right, int[] temp) {
        int i = left; // 左边序列的初始索引
        int j = mid + 1; // 右边序列的初始索引
        int t = 0; // 指向temp[]数组的当前索引

        // 先把左右两边的（有序）的数据按照规则填充到temp数组
        // 知道左右两边的有序序列，有一边处理完毕为止
        while (i <= mid && j <= right) {
            if (arr[i] <= arr[j]) {
                temp[t] = arr[i];
                t++;
                i++;
            } else {
                temp[t] = arr[j];
                t++;
                j++;
            }
        }

        // 2.
        // 把剩余一边的数据依次填充到temp
        while (i <= mid) {
            temp[t] = arr[i];
            t++;
            i++;
        }
        while (j <= right) {
            temp[t] = arr[j];
            t++;
            j++;
        }

        // 3.
        // 将temp数组的元素拷贝到arr
        // 并不是每次都拷贝所有
        t = 0;
        int tempLeft = left;
        while (tempLeft <= right) {
            arr[tempLeft] = temp[t];
            t++;
            tempLeft++;
        }
    }
}
```



## 1.11 基数排序

### 1.11.1 基数排序(桶排序)介绍:

1) 基数排序（radix sort）属于“分配式排序”（distribution sort）， 又称“桶子法”（bucket sort）或 bin sort， 顾 名思义，它是通过键值的各个位的值，将要排序的元素分配至某些“桶”中，达到排序的作用

2) 基数排序法是属于稳定性的排序，基数排序法的是效率高的稳定性排序法
   1) `{3, 1, 43, 1}` -> `{1, 1, 3, 43}`: 稳定性排序是指原先在前面的1排序后仍然在前

3) 基数排序(Radix Sort)是桶排序的扩展

4) 基数排序是 1887 年赫尔曼·何乐礼发明的。它是这样实现的：将整数按位数切割成不同的数字，然后按每个位数分别比较。

### 1.11.2 基数排序基本思想

1) 将所有待比较数值统一为同样的数位长度，数位较短的数前面补零。然后，从最低位开始，依次进行一次排序。 这样从最低位排序一直到最高位排序完成以后, 数列就变成一个有序序列。
1) 这样说明，比较难理解，下面我们看一个图文解释，理解基数排序的步骤

### 1.11.3 基数排序图文说明

将数组` {53, 3, 542, 748, 14, 214} `使用基数排序, 进行升序排序

<img src = "https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/data_structure_imgs/基数排序01.png?raw=true" alt = "基数排序01" style = "zoom : 50%"/>

<img src = "https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/data_structure_imgs/基数排序02.png?raw=true" alt = "基数排序02" style = "zoom : 50%"/>

### 1.11.4 基数排序代码实现

要求：将数组 `{53, 3, 542, 748, 14, 214} `使用基数排序, 进行升序排序

```jave
package com.chenghao;

import java.lang.reflect.Array;
import java.util.Arrays;

public class RadixSort {
    public static void main(String[] args) {
        int[] arr = {53, 3, 542, 748, 14, 214};
        radixSort(arr);

    }

    public static void radixSort(int[] arr) {
        // 得到最大位数
        int max = arr[0];
        for (int i = 1; i < arr.length; i++) {
            if (arr[i] > max) {
                max = arr[i];
            }
        }
        // 最大数是几位数
        int maxLength = (max + "").length();

        // 定义一个二维数组表示十个桶，每个桶就是一个一维数组
        int[][] bucket = new int[10][arr.length];

        // 为了记录每个桶实际存放了多少数据，我们定义一个一维数组来记录各个桶每次放入了多少个数据
        int[] bucketElementCounts = new int[10];

        for (int i = 0, n = 1; i < maxLength; i++, n *= 10) {
            for (int j = 0; j < arr.length; j++) {
                // 取出每个元素的个位
                int digitOfElement = arr[j] / n % 10;
                bucket[digitOfElement][bucketElementCounts[digitOfElement]] = arr[j];
                bucketElementCounts[digitOfElement]++;
            }
            // 取出数据
            int index = 0;
            for (int k = 0; k < bucketElementCounts.length; k++) {
                // 如果桶中有数据，我们才放入到原数组
                if (bucketElementCounts[k] != 0) {
                    for (int l = 0; l < bucketElementCounts[k]; l++) {
                        arr[index] = bucket[k][l];
                        index++;
                    }
                }
                bucketElementCounts[k] = 0;
            }
            System.out.println("第" + (i + 1) + "轮排序后的数组：" + Arrays.toString(arr));
        }

//
//        // 2. (针对每个元素的十位)
//
//        for (int j = 0; j < arr.length; j++) {
//            // 取出每个元素的个位
//            int digitOfElement = arr[j] / 10 % 10;
//            bucket[digitOfElement][bucketElementCounts[digitOfElement]] = arr[j];
//            bucketElementCounts[digitOfElement]++;
//        }
//        // 取出数据
//        index = 0;
//        for (int k = 0; k < bucketElementCounts.length; k++) {
//            // 如果桶中有数据，我们才放入到原数组
//            if (bucketElementCounts[k] != 0) {
//                for (int l = 0; l < bucketElementCounts[k]; l++) {
//                    arr[index] = bucket[k][l];
//                    index++;
//                }
//            }
//            bucketElementCounts[k] = 0;
//        }
//        System.out.println(Arrays.toString(arr));
//
//        // 3. (针对每个元素的百位)
//
//        for (int j = 0; j < arr.length; j++) {
//            // 取出每个元素的个位
//            int digitOfElement = arr[j] / 100 % 10;
//            bucket[digitOfElement][bucketElementCounts[digitOfElement]] = arr[j];
//            bucketElementCounts[digitOfElement]++;
//        }
//        // 取出数据
//        index = 0;
//        for (int k = 0; k < bucketElementCounts.length; k++) {
//            // 如果桶中有数据，我们才放入到原数组
//            if (bucketElementCounts[k] != 0) {
//                for (int l = 0; l < bucketElementCounts[k]; l++) {
//                    arr[index] = bucket[k][l];
//                    index++;
//                }
//            }
//            bucketElementCounts[k] = 0;
//        }
//        System.out.println(Arrays.toString(arr));
    }
}
```

### 1.11.5 基数排序的说明:

1) 基数排序是对传统桶排序的扩展，速度很快.

2) 基数排序是经典的空间换时间的方式，占用内存很大, 当对海量数据排序时，容易造成 OutOfMemoryError 。

3) 基数排序时稳定的。 [注:假定在待排序的记录序列中， 存在多个具有相同的关键字的记录，若经过排序，这些 记录的相对次序保持不变，即在原序列中， r[i]=r[j]， 且 r[i]在 r[j]之前， 而在排序后的序列中， r[i]仍在 r[j]之前， 则称这种排序算法是稳定的；否则称为不稳定的]

4) 有负数的数组，我们不用基数排序来进行排序, 如果要支持负数，参考: https://code.i-harness.com/zh-CN/q/e98fa9



## 1.12 常用排序算法总结和对比

### 1.12.1 一张排序算法的比较图

<img src = "https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/data_structure_imgs/排序17.png?raw=true" alt = "排序17" style = "zoom : 50%"/>



### 1.12.2 相关术语解释：

1) 稳定：如果 a 原本在 b 前面，而 a=b，排序之后 a 仍然在 b 的前面；

2) 不稳定：如果 a 原本在 b 的前面，而 a=b，排序之后 a 可能会出现在 b 的后面；

3) 内排序：所有排序操作都在内存中完成；

4) 外排序：由于数据太大，因此把数据放在磁盘中，而排序通过磁盘和内存的数据传输才能进行；

5) 时间复杂度： 一个算法执行所耗费的时间。

6) 空间复杂度：运行完一个程序所需内存的大小。

7) n: 数据规模

8) k: “桶”的个数

9) In-place: 不占用额外内存

10) Out-place: 占用额外内存

---
layout:     post
title:      "数据结构与算法 07-查找算法"
subtitle:   
date:       2023-07-09 16:00
author:     "Chenghao"
header-img: "img/post-bg-2015.jpg"
tags:
    - 数据结构与算法
    - Java
---

> 笔记摘自：尚硅谷 | B站视频链接：<https://www.bilibili.com/video/BV1E4411H73v/>



## 1.1 查找算法介绍

在 java 中，我们常用的查找有四种:

1) 顺序(线性)查找

2) 二分查找/折半查找

3) 插值查找

4) 斐波那契查找

## 1.2 线性查找算法

有一个数列： `{1,8, 10, 89, 1000, 1234}` ，判断数列中是否包含此名称【顺序查找】 要求: 如果找到了，就提 示找到，并给出下标值。

```java
public class SeqSearch {
    public static void main(String[] args) {
        int[] arr = {1, 9, 11, -1, 34, 89};
        int index = seqSearch(arr, 11);
        if (index == -1) {
            System.out.println("没有查找到");
        } else {
            System.out.println("找到，下标为 " + index);
        }
    }
    
    public static int seqSearch(int[] arr, int value) {
        for (int i = 0; i < arr.length; i++) {
            if (arr[i] == value) {
                return i;
            }
        }
        return -1;
    }
}
```



## 1.3 二分查找算法

### 1.3.1二分查找：

请对一个**有序数组**进行二分查找 标，如果没有就提示"没有这个数"。

### 1.3.2二分查找算法的思路

<img src = "https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/data_structure_imgs/查找01.png?raw=true" alt = "查找01" style = "zoom :50%"/>

### 1.3.3二分查找的代码

```java
package com.chenghao;

import java.util.ArrayList;
import java.util.List;

public class BinarySearch {
    public static void main(String[] args) {
        int[] arr = {1, 8, 10, 89, 1000, 1000, 1000, 1234};
//        int index = binarySearch(arr, 0, arr.length - 1, 10);
        List<Integer> index = binarySearch2(arr, 0, arr.length - 1, 1000);
        System.out.println(index);
    }

    public static int binarySearch(int[] arr, int left, int right, int value) {
        while (left <= right) {
            int mid = left + (right - left) / 2;
            if (arr[mid] == value) {
                return mid;
            } else if (arr[mid] < value) {
                left = mid + 1;
                binarySearch(arr, left, right, value);
            } else if (arr[mid] > value) {
                right = mid - 1;
                binarySearch(arr, left, right, value);
            }
        }
        return -1;
    }

    public static List<Integer> binarySearch2(int[] arr, int left, int right, int value) {
        while (left <= right) {
            int mid = left + (right - left) / 2;
            if (arr[mid] == value) {
                List<Integer> list = new ArrayList<>();
                int temp = mid - 1;
                while (true) {
                    if (temp < 0 || arr[temp] != value) {
                        break;
                    } else {
                        list.add(temp);
                        temp--;
                    }
                }
                list.add(mid);
                temp = mid + 1;
                while (true) {
                    if (temp > arr.length - 1 || arr[temp] != value) {
                        break;
                    } else {
                        list.add(temp);
                        temp++;
                    }
                }
                return list;
            } else if (arr[mid] < value) {
                left = mid + 1;
                binarySearch(arr, left, right, value);
            } else if (arr[mid] > value) {
                right = mid - 1;
                binarySearch(arr, left, right, value);
            }
        }
        return new ArrayList<>();
    }
}
```



## 1.4 插值查找算法

插值查找原理介绍:

1. 插值查找算法类似于二分查找，不同的是插值查找每次从自适应 `mid `处开始查找。

2. 将折半查找中的求 `mid` 索引的公式 , `low` 表示左边索引 `left`，`high` 表示右边索引 `right`. `key` 就是前面我们讲的 `findVal`
   $$
   mid = \frac{low+high}{2} = low + \frac{1}{2}(high - low) \ \ \rightarrow \ \ mid = low + \frac{key - a[low]}{a[high] - a[low]}(high - low)
   $$

3. `int mid = low + (high - low) * (key - arr[low]) / (arr[high] - arr[low])` 

   对应前面的代码公式：` int mid = left + (right – left) * (findVal – arr[left]) / (arr[right] – arr[left])`

4. 举例说明插值查找算法 1-100 的数组

   <img src = "https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/data_structure_imgs/查找02.png?raw=true" alt = "查找02" style = "zoom :50%"/>

### 1.4.1插值查找应用案例：

```java
import java.util.Arrays;

public class InsertSearch {
    public static void main(String[] args) {
        int[] arr = new int[100];
        for (int i = 0; i < 100; i++) {
            arr[i] = i + 1;
        }
        int index = insertSearch(arr, 0, arr.length - 1, 99);
        System.out.println(index);
    }

    public static int insertSearch(int[] arr, int left, int right, int findVal) {
        if (left > right || findVal < arr[left] || findVal > arr[right]) {
            return -1;
        }
        int mid = left + (right - left) * (findVal - arr[left]) / (arr[right] - arr[left]);
        if (arr[mid] == findVal) {
            return mid;
        } else if (arr[mid] < findVal) {
            insertSearch(arr, mid + 1, right, findVal);
        } else {
            insertSearch(arr, left, mid - 1, findVal);
        }
        return 0;
    }
}
```



### 1.4.2插值查找注意事项：

1) 对于数据量较大，关键字分布比较均匀的查找表来说，采用插值查找, 速度较快.

2) 关键字分布不均匀的情况下，该方法不一定比折半查找要好



## 1.5 斐波那契(黄金分割法)查找算法

### 1.5.1 斐波那契(黄金分割法)查找基本介绍:

1) 黄金分割点是指把一条线段分割为两部分，使其中一部分与全长之比等于另一部分与这部分之比。取其前三位 数字的近似值是 0.618。由于按此比例设计的造型十分美丽，因此称为黄金分割，也称为中外比。这是一个神 奇的数字，会带来意向不大的效果。

2) 斐波那契数列 {1, 1, 2, 3, 5, 8, 13, 21, 34, 55 } 发现斐波那契数列的两个相邻数 的比例，无限接近黄金分割值0.618

### 1.5.2 斐波那契(黄金分割法)原理:

斐波那契查找原理与前两种相似，仅仅改变了中间结点（mid）的位置，mid 不再是中间或插值得到，而是位 于黄金分割点附近，即 `mid=low+F(k-1)-1`（F 代表斐波那契数列），如下图所示

<img src = "https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/data_structure_imgs/查找03.png?raw=true" alt = "查找03" style = "zoom :50%"/>

对 F(k-1)-1 的理解： 

1.  由斐波那契数列 `F[k]=F[k-1]+F[k-2] `的性质，可以得到 `(F[k] - 1)=(F[k - 1] - 1) +(F[k - 2] - 1) + 1 `。该式说明： 只要顺序表的长度为 `F[k]-1`，则可以将该表分成长度为 `F[k-1]-1` 和 `F[k-2]-1` 的两段，即如上图所示。从而中间位置为 `mid = low + F(k - 1) - 1`

2. 类似的，每一子段也可以用相同的方式分割

3. 但顺序表长度 n 不一定刚好等于 F[k]-1，所以需要将原来的顺序表长度 n 增加至 F[k]-1。这里的 k 值只要能使 得 F[k]-1 恰好大于或等于 n 即可，由以下代码得到,顺序表长度增加后，新增的位置（从 n+1 到 F[k]-1 位置）， 都赋为 n 位置的值即可。

   ```java
   while (n > fib(k) - 1) {
   		k++;
   }
   ```



### 1.5.3 斐波那契查找应用案例：

```java
package com.chenghao;

import java.util.Arrays;

public class FibonaciSearch {
    public static int maxSize = 20;

    public static void main(String[] args) {
        int[] arr = {1, 8, 10, 89, 1000, 1234};

    }

    public static int[] fib() {
        int[] f = new int[maxSize];
        f[0] = 1;
        f[1] = 1;
        for (int i = 2; i < maxSize; i++) {
            f[i] = f[i - 1] + f[i - 2];
        }
        return f;
    }

    public static int fibSearch(int[] a, int key) {
        int low = 0;
        int high = a.length - 1;
        int k = 0; // 斐波那契分割数值的下标

        int mid = 0;
        int[] f = fib();

        while (high > f[k] - 1) {
            k++;
        }
        int[] temp = Arrays.copyOf(a, f[k]);
        for (int i = high + 1; i < temp.length; i++) {
            temp[i] = a[high];
        }

        while (low <= high) {
            mid = low + f[k - 1] - 1;
            if (key < temp[mid]) {
                high = mid - 1;
                k--;
            } else if (key > temp[mid]) {
                low = mid + 1;
                k -= 2;
            } else {
                if (mid <= high) {
                    return mid;
                 } else {
                    return high;
                }
            }
        }
        return -1;
    }
}
```


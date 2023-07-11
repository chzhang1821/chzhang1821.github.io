---
layout:     post
title:      "数据结构与算法 08-哈希表"
subtitle:   
date:       2023-07-09 16:00
author:     "Chenghao"
header-img: "img/post-bg-2015.jpg"
tags:
    - 数据结构与算法
    - Java
---

> 笔记摘自：尚硅谷 | B站视频链接：<https://www.bilibili.com/video/BV1E4411H73v/>

## 1.1 哈希表(散列)-Google 上机题

1) 看一个实际需求，google 公司的一个上机题:

2) 有一个公司,当有新的员工来报道时,要求将该员工的信息加入(id,性别,年龄,住址..),当输入该员工的 id 时,要求查 找到该员工的 所有信息.

3) 要求: 不使用数据库,尽量节省内存,速度越快越好=>哈希表(散列)

## 1.2 哈希表的基本介绍

散列表（Hash table，也叫哈希表），是根据关键码值(Key value)而直接进行访问的数据结构。也就是说，它通 过把关键码值映射到表中一个位置来访问记录，以加快查找的速度。这个映射函数叫做散列函数，存放记录的数组 叫做散列表。

<img src = "https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/data_structure_imgs/哈希表01.png?raw=true" alt = "哈希表01" style = "zoom :50%"/>



## 1.3 google 公司的一个上机题:

有一个公司,当有新的员工来报道时,要求将该员工的信息加入(id,性别,年龄,名字,住址..),当输入该员工的 id 时, 要求查找到该员工的 所有信息.

要求:

1) 不使用数据库，速度越快越好 => 哈希表(散列)

2) 添加时，保证按照 id 从低到高插入 [课后思考：如果 id 不是从低到高插入，但要求各条链表仍是从低到 高，怎么解决?]

3) 使用链表来实现哈希表, 该链表不带表头[即: 链表的第一个结点就存放雇员信息]

4) 思路分析并画出示意图

<img src = "https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/data_structure_imgs/哈希表02.png?raw=true" alt = "哈希表02" style = "zoom :50%"/>

```java
public class Hash {
    public static void main(String[] args) {
        HashTab hashTab = new HashTab(7);
        String key = "";
        Scanner scanner = new Scanner(System.in);
        while (true) {
            System.out.println("add: 添加雇员");
            System.out.println("list: 添加雇员");
            System.out.println("find: 查找雇员");
            System.out.println("exit: 退出系统");
            key = scanner.next();

            switch (key) {
                case "add":
                    System.out.println("输入id");
                    int id = scanner.nextInt();
                    System.out.println("输入名字");
                    String name = scanner.next();
                    Emp emp = new Emp(id, name);
                    hashTab.add(emp);
                    break;
                case "list":
                    hashTab.list();
                    break;
                case "find":
                    System.out.println("请输入要查找的id");
                    id = scanner.nextInt();
                    hashTab.findEmpById(id);
                    break;
                case "exit":
                    scanner.close();
                    System.exit(0);
                default:
                    break;
            }
        }
    }
}

class Emp {
    public int id;
    public String name;
    public Emp next;
    public Emp(int id, String name) {
        this.id = id;
        this.name = name;
    }
}

class EmpLinkedList {
    private Emp head;

    public void add(Emp emp) {
        if (head == null) {
            head = emp;
            return;
        }
        Emp curEmp = head;
        while (curEmp.next != null) {
            curEmp = curEmp.next;
        }
        curEmp.next = emp;
    }
    public void list(int no) {
        if (head == null) {
            System.out.println("第 " + (no + 1) + " 当前链表为空");
            return;
        }
        System.out.println("第 " + (no + 1) + " 条链表的信息为：");
        Emp curEmp = head;
        while (curEmp != null) {
            System.out.printf("=> id = %d, name = %s\n", curEmp.id, curEmp.name);
            curEmp = curEmp.next;
        }
    }

    public Emp findEmpById(int id) {
        if (head == null) {
            System.out.println("链表为空");
            return null;
        }
        Emp curEmp = head;
        while (curEmp != null) {
            if (curEmp.id == id) {
                break;
            }
            curEmp = curEmp.next;
        }
        return curEmp;
    }
}

class HashTab {
    private EmpLinkedList[] empLinkedListArray;
    private int size;

    public HashTab(int size) {
        this.size = size;
        empLinkedListArray = new EmpLinkedList[size];
        for (int i = 0; i < size; i++) {
            empLinkedListArray[i] = new EmpLinkedList();
        }
    }

    public int hashFun(int id) {
        return id % size;
    }

    public void add(Emp emp) {
        int empLinkedListNO = hashFun(emp.id);
        empLinkedListArray[empLinkedListNO].add(emp);
    }

    public void list() {
        for (int i = 0; i < size; i++) {
            empLinkedListArray[i].list(i);
        }
    }

    public void findEmpById(int id) {
        int empLinkedListNO = hashFun(id);
        Emp emp = empLinkedListArray[empLinkedListNO].findEmpById(id);
        if (emp != null) {
            System.out.printf("在第%d条链表中找到 雇员id = %d\n", empLinkedListNO + 1, id);
        } else {
            System.out.println("在哈希表中，没有找到该雇员");
        }
    }
}
```


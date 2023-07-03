---
layout:     post
title:      "数据结构与算法 03-链表"
subtitle:   
date:       2023-06-30 16:00
author:     "Chenghao"
header-img: "img/post-bg-2015.jpg"
tags:
    - 数据结构与算法
    - Java
---

> 笔记摘自：尚硅谷 | B站视频链接：<https://www.bilibili.com/video/BV1E4411H73v/>

# 链表

## 1. 链表介绍

链表是有序的列表，但是它在内存中是存储如下

<img src = "/Users/chenghaozhang/Library/CloudStorage/OneDrive-Personal/MyBlog/chzhang1821.github.io/img/data_structure_imgs/链表01.png?raw=true" alt = "链表01" style = "zoom: 50%"/>

小结上图:

1) 链表是以节点的方式来存储,是链式存储
2) 每个节点包含 data 域， next 域：指向下一个节点.
3) 如图：发现链表的各个节点不一定是连续存储.
4) 链表分带头节点的链表和没有头节点的链表，根据实际的需求来确定 

>  实例：客户端定时向服务器发送请求，根据发送的好友编号例如 (20, 1,19,30) 要求返回好友状态，且按从小到大的顺序排列 (1,19,20,30)

单链表(带头结点) 逻辑结构示意图如下

<img src = "/Users/chenghaozhang/Library/CloudStorage/OneDrive-Personal/MyBlog/chzhang1821.github.io/img/data_structure_imgs/链表02.png?raw=true" alt = "链表02" style = "zoom: 50%"/>

## 2. 单链表的应用实例

使用带 head 头的单向链表实现 –水浒英雄排行榜管理完成对英雄人物的增删改查操作， 注: 删除和修改,查找 可以考虑学员独立完成，也可带学员完成

1) 第一种方法在添加英雄时，直接添加到链表的尾部 思路分析示意图:

思路分析示意图：

<img src = "/Users/chenghaozhang/Library/CloudStorage/OneDrive-Personal/MyBlog/chzhang1821.github.io/img/data_structure_imgs/链表03.png?raw=true" alt = "链表03" style = "zoom: 50%"/>

2) 第二种方式在添加英雄时，根据排名将英雄插入到指定位置(如果有这个排名，则添加失败，并给出提示) 思路的分析示意图:

<img src = "/Users/chenghaozhang/Library/CloudStorage/OneDrive-Personal/MyBlog/chzhang1821.github.io/img/data_structure_imgs/链表04.png?raw=true" alt = "链表04" style = "zoom: 50%"/>

3) 修改节点功能

思路(1) 先找到该节点，通过遍历，(2) `temp.name = newHeroNode.name` ; `temp.nickname = newHeroNode.nickname`

4) 删除节点

思路分析的示意图:

<img src = "/Users/chenghaozhang/Library/CloudStorage/OneDrive-Personal/MyBlog/chzhang1821.github.io/img/data_structure_imgs/链表05.png?raw=true" alt = "链表05" style = "zoom: 50%"/>

5. 代码演示：

```java
package com.chenghao;

public class SingleLinkedListDemo {
    public static void main( String[] args ) {
        // 测试
        HeroNode hero1 = new HeroNode(1, "宋江", "及时雨");
        HeroNode hero2 = new HeroNode(2, "卢俊义", "玉麒麟");
        HeroNode hero3 = new HeroNode(3, "吴用", "智多星");
        HeroNode hero4 = new HeroNode(4, "林冲", "豹子头");

        SingleLinkedList singleLinkedList = new SingleLinkedList();
//        singleLinkedList.add(hero1);
//        singleLinkedList.add(hero2);
//        singleLinkedList.add(hero3);
//        singleLinkedList.add(hero4);

        singleLinkedList.addByOrder(hero1);
        singleLinkedList.addByOrder(hero4);
        singleLinkedList.addByOrder(hero3);
        singleLinkedList.addByOrder(hero2);

        singleLinkedList.list();
        System.out.println("--------------------");

        // 修改节点
        HeroNode newHeroNode = new HeroNode(2, "小卢", "玉麒麟~~");
        singleLinkedList.update(newHeroNode);
        singleLinkedList.list();
        System.out.println("--------------------");

        // 删除节点
        singleLinkedList.remove(1);
        singleLinkedList.remove(2);
        singleLinkedList.remove(4);
        singleLinkedList.list();
    }
}

class HeroNode {
    public int no;
    public String name;
    public String nickname;
    public HeroNode next;

    public HeroNode() {}
    public HeroNode(int hNo, String hName, String hNickname) {
        this.no = hNo;
        this.name = hName;
        this.nickname = hNickname;
    }

    @Override
    public String toString() {
        return "HeroNode{" +
                "no=" + no +
                ", name='" + name + '\'' +
                ", nickname='" + nickname + '\'' +
                '}';
    }
}

class SingleLinkedList {
    private HeroNode head = new HeroNode(0, "", "");

    // 添加节点到单向链表
    // 思路：当不考虑编号顺序时
    // 1. 找到当前链表的最后节点
    // 2. 将最后这个节点的next 指向新的节点
    public void add(HeroNode heroNode) {
        // 因为head不能动，定义一个temp节点来遍历链表
        HeroNode temp = head;
        // 遍历
        while (temp.next != null) {
            temp = temp.next;
        }
        // 当退出while循环时，temp指向了链表最后
        temp.next = heroNode;
    }

    public void addByOrder(HeroNode heroNode) {
        // 因为head不能动，定义一个temp节点来遍历链表
        HeroNode temp = head;
        boolean flag = false; // 标志添加的编号是否存在，默认为false
        // 遍历
        while (temp.next != null) {
            if (heroNode.no < temp.next.no) {
                break;
            } else if (heroNode.no == temp.next.no) {
                flag = true;
                break;
            }
            temp = temp.next;
        }

        if (flag) {
            System.out.printf("准备插入的英雄的编号%d已存在\n", heroNode.no);
        } else {
            heroNode.next = temp.next;
            temp.next = heroNode;
        }
    }

    // 修改节点信息，根据编号no来修改，即no不能改
    public void update(HeroNode newHeroNode) {
        if (head.next == null) {
            System.out.println("链表为空~");
            return;
        }
        HeroNode temp = head.next;
        boolean flag = false;
        while (temp != null) {
            if (temp.no == newHeroNode.no) {
                flag = true;
                break;
            }
            temp = temp.next;
        }

        if (flag) {
            temp.name = newHeroNode.name;
            temp.nickname = newHeroNode.nickname;
        } else {
            System.out.printf("没有找到 编号%d 这个节点\n", newHeroNode.no);
        }
    }

    // 删除节点
    public void remove(int no) {
        HeroNode temp = head;
        boolean flag = false;

        while (temp.next != null) {
            if (temp.next.no == no) {
                flag = true;
                break;
            }
            temp = temp.next;
        }

        if (flag) {
            temp.next = temp.next.next;
        } else {
            System.out.printf("没有找到编号为%d的节点\n", no);
        }
    }

    // 显示链表
    public void list() {
        if (head.next == null) {
            System.out.println("链表为空");
        }
        HeroNode temp = head.next;
        while (temp != null) {
            System.out.println(temp);
            temp = temp.next;
        }
    }
}
```



## 3. 双向链表

### 3.1 双向链表的操作分析和实现

使用带 head 头的双向链表实现 –水浒英雄排行榜 

管理单向链表的缺点分析:

1) 单向链表，查找的方向只能是一个方向，而双向链表可以向前或者向后查找。

2) 单向链表不能自我删除，需要靠辅助节点 ，而双向链表，则可以自我删除，所以前面我们单链表删除 时节点，总是找到 temp,temp 是待删除节点的前一个节点(认真体会).

3) 分析了双向链表如何完成遍历，添加，修改和删除的思路

<img src = "/Users/chenghaozhang/Library/CloudStorage/OneDrive-Personal/MyBlog/chzhang1821.github.io/img/data_structure_imgs/双向链表01.png?raw=true" alt = "双向链表01" style = "zoom: 50%"/>

对上图的说明:

分析 双向链表的遍历，添加，修改，删除的操作思路===》代码实现

1) **遍历** 和 单链表一样，只是可以向前，也可以向后查找

2) **添加** (默认添加到双向链表的最后)
   1) 先找到双向链表的最后这个节点
   2) temp.next = newHeroNode
   3) newHeroNode.pre = temp;


3) 修改 思路和 原来的单向链表一样.

4) 删除
   1) 因为是双向链表，因此，我们可以实现自我删除某个节点
   2) 直接找到要删除的这个节点，比如 temp
   3) ``temp.pre.next = temp.next``
   4) ``temp.next.pre = temp.pre`


代码实现

```java
package com.chenghao;

public class DoubleLinkedListDemo {
    public static void main(String[] args) {
        DoubleLinkedList doubleLinkedList = new DoubleLinkedList();
        HeroNode2 head = new HeroNode2();
        HeroNode2 hero1 = new HeroNode2(1, "宋江", "及时雨");
        HeroNode2 hero2 = new HeroNode2(2, "卢俊义", "玉麒麟");
        HeroNode2 hero3 = new HeroNode2(3, "吴用", "智多星");
        HeroNode2 hero4 = new HeroNode2(4, "林冲", "豹子头");

//        doubleLinkedList.add(hero1);
//        doubleLinkedList.add(hero2);
//        doubleLinkedList.add(hero3);
//        doubleLinkedList.add(hero4);
//
//        doubleLinkedList.list();
//        System.out.println("--------------------");

        doubleLinkedList.addByOrder(hero1);
        doubleLinkedList.addByOrder(hero3);
        doubleLinkedList.addByOrder(hero2);
        doubleLinkedList.addByOrder(hero4);

        doubleLinkedList.list();
        System.out.println("--------------------");

        HeroNode2 hero5 = new HeroNode2(4, "公孙胜", "入云龙");
        doubleLinkedList.update(hero5);
        doubleLinkedList.list();
        System.out.println("--------------------");

        doubleLinkedList.remove(4);
        doubleLinkedList.list();
        System.out.println("--------------------");
    }
}


class HeroNode2 {
    public int no;
    public String name;
    public String nickname;
    public HeroNode2 next;
    public HeroNode2 pre;

    public HeroNode2() {}
    public HeroNode2(int hNo, String hName, String hNickname) {
        this.no = hNo;
        this.name = hName;
        this.nickname = hNickname;
    }

    @Override
    public String toString() {
        return "HeroNode{" +
                "no=" + no +
                ", name='" + name + '\'' +
                ", nickname='" + nickname + '\'' +
                '}';
    }
}

class DoubleLinkedList {
    private HeroNode2 head = new HeroNode2(0, "", "");

    // 添加节点到双向链表最后
    public void add(HeroNode2 heroNode) {
        // 因为head不能动，定义一个temp节点来遍历链表
        HeroNode2 temp = head;
        // 遍历
        while (temp.next != null) {
            temp = temp.next;
        }
        // 当退出while循环时，temp指向了链表最后
        temp.next = heroNode;
        heroNode.pre = temp;
    }

    public void addByOrder(HeroNode2 heroNode) {
        // 因为head不能动，定义一个temp节点来遍历链表
        HeroNode2 temp = head;
        boolean flag = false; // 标志添加的编号是否存在，默认为false
        // 遍历
        while (temp.next != null) {
            if (heroNode.no < temp.next.no) {
                break;
            } else if (heroNode.no == temp.next.no) {
                flag = true;
                break;
            }
            temp = temp.next;
        }

        if (flag) {
            System.out.printf("准备插入的英雄的编号%d已存在\n", heroNode.no);
        } else {
            heroNode.next = temp.next;
            if (temp.next != null) {
                temp.next.pre = heroNode;
            }
//            temp.next.pre = heroNode;
            temp.next = heroNode;
            heroNode.pre = temp;
        }
    }

    // 修改节点信息，根据编号no来修改，即no不能改
    public void update(HeroNode2 newHeroNode) {
        if (head.next == null) {
            System.out.println("链表为空~");
            return;
        }
        HeroNode2 temp = head.next;
        boolean flag = false;
        while (temp != null) {
            if (temp.no == newHeroNode.no) {
                flag = true;
                break;
            }
            temp = temp.next;
        }

        if (flag) {
            temp.name = newHeroNode.name;
            temp.nickname = newHeroNode.nickname;
        } else {
            System.out.printf("没有找到 编号%d 这个节点\n", newHeroNode.no);
        }
    }

    // 删除节点
    public void remove(int no) {
        if (head.next == null) {
            System.out.println("链表为空");
            return;
        }
        HeroNode2 temp = head.next;
        boolean flag = false;

        while (temp != null) {
            if (temp.no == no) {
                flag = true;
                break;
            }
            temp = temp.next;
        }

        if (flag) {
            temp.pre.next = temp.next;
            if (temp.next != null) {
                temp.next.pre = temp.pre;
            }
        } else {
            System.out.printf("没有找到编号为%d的节点\n", no);
        }
    }

    // 显示链表
    public void list() {
        if (head.next == null) {
            System.out.println("链表为空");
        }
        HeroNode2 temp = head.next;
        while (temp != null) {
            System.out.println(temp);
            temp = temp.next;
        }
    }

    public int count() {
        if (head.next == null) {
            return 0;
        }
        HeroNode2 temp = head.next;
        int count = 0;
        while (temp != null) {
            temp = temp.next;
            count++;
        }
        return count;
    }
}
```


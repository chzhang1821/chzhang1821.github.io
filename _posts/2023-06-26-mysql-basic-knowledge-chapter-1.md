---
layout:     post
title:      "MySQL 基础知识01 - 数据库概述"
subtitle:   
date:       2023-06-26 16:00
author:     "Chenghao"
header-img: "img/post-bg-2015.jpg"
tags:
    - MySQL
---

> 笔记摘自：尚硅谷-宋红康 | b站视频链接：<https://www.bilibili.com/video/BV1iq4y1u7vj/>

- [第01章：数据库概述](#第01章数据库概述)
  - [1. 为什么要使用数据库](#1-为什么要使用数据库)
  - [2. 数据库与数据库管理系统](#2-数据库与数据库管理系统)
    - [2.1 数据库的相关概念](#21-数据库的相关概念)
    - [2.2 数据库与数据库管理系统的关系](#22-数据库与数据库管理系统的关系)
  - [3. RDBMS 与 非RDBMS](#3-rdbms-与-非rdbms)
    - [3.1 关系型数据库（RDBMS）](#31-关系型数据库rdbms)
    - [3.2 非关系型数据库（非RDBMS）](#32-非关系型数据库非rdbms)
  - [4. 关系型数据库设计规则](#4-关系型数据库设计规则)
    - [4.1 表、记录、字段](#41-表记录字段)
    - [4.2 表的关联关系](#42-表的关联关系)



## 第01章：数据库概述

### 1. 为什么要使用数据库
* 持久化(persistence)：**把数据保存到可掉电式存储设备中以供之后使用**。大多数情况下，特别是企业级应用，**数据持久化意味着将内存中的数据保存到硬盘上加以“固化”**，而持久化的实现过程大多通过各种关系数据库来完成。
* 持久化的主要作用是**将内存中的数据存储在关系型数据库中**，当然也可以存储在磁盘文件、XML数据文件中

<img src="https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/mysql_imgs/image-20211020202152071.png?raw=true" alt="image-20211020202152071" style="zoom:50%;" />


### 2. 数据库与数据库管理系统

#### 2.1 数据库的相关概念

| **DB：数据库（Database）**                                   |
| ------------------------------------------------------------ |
| 即存储数据的“仓库”，其本质是一个文件系统。它保存了一系列有组织的数据。 |
| **DBMS：数据库管理系统（Database Management System）**       |
| 是一种操纵和管理数据库的大型软件，用于建立、使用和维护数据库，对数据库进行统一管理和控制。用户通过数据库管理系统访问数据库中表内的数据。 |
| **SQL：结构化查询语言（Structured Query Language）**         |
| 专门用来与数据库通信的语言。                                 |

#### 2.2 数据库与数据库管理系统的关系
数据库管理系统(DBMS)可以管理多个数据库，一般开发人员会针对每一个应用创建一个数据库。为保存应用中实体的数据，一般会在数据库创建多个表，以保存程序中实体用户的数据。 

数据库管理系统、数据库和表的关系如图所示：
<img src="https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/mysql_imgs/image-20211013202511233.png?raw=true" alt="image-20211013202511233" style="zoom:50%;" />

<img src="https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/mysql_imgs/image-20210915112546261.png?raw=true" alt="image-20210915112546261" style="zoom:100%;" />

### 3. RDBMS 与 非RDBMS
从排名中我们能看出来，关系型数据库绝对是 DBMS 的主流，其中使用最多的 DBMS 分别是 Oracle、MySQL 和 SQL Server。这些都是关系型数据库（Relational Database Management System - RDBMS）。

#### 3.1 关系型数据库（RDBMS）
##### 3.1.1 实质
- 这种类型的数据库是`最古老`的数据库类型，关系型数据库模型是把复杂的数据结构归结为简单的`二元关系`（即二维表格形式）。

<img src="https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/mysql_imgs/image-20211020145811031.png?raw=true" alt="image-20211020145811031" style="zoom:90%;" />

- 关系型数据库以`行(row)`和`列(column)`的形式存储数据，以便于用户理解。这一系列的行和列被称为`表(table)`，一组表组成了一个库(database)。

- 表与表之间的数据记录有关系(relationship)。现实世界中的各种实体以及实体之间的各种联系均用`关系模型`来表示。关系型数据库，就是建立在`关系模型`基础上的数据库。

- SQL 就是关系型数据库的查询语言。

<img src="https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/mysql_imgs/image-20210914235413708.png?raw=true" alt="image-20210914235413708" style="zoom:90%;" />

##### 3.1.2 优势
* 复杂查询 
  
  可以用SQL语句方便的在一个表以及多个表之间做非常复杂的数据查询。

* 事务支持
  
  使得对于安全性能很高的数据访问要求得以实现

#### 3.2 非关系型数据库（非RDBMS）

##### 3.2.1 介绍

**非关系型数据库**，可看成传统关系型数据库的功能`阉割版本`，基于键值对存储数据，不需要经过SQL层的解析，`性能非常高`。同时，通过减少不常用的功能，进一步提高性能。

目前基本上大部分主流的非关系型数据库都是免费的。

##### 3.2.2 有哪些非关系型数据库

相比于 SQL，NoSQL 泛指非关系型数据库，包括了榜单上的键值型数据库、文档型数据库、搜索引擎和列存储等，除此以外还包括图形数据库。也只有用 NoSQL 一词才能将这些技术囊括进来。

**键值型数据库**

键值型数据库通过 Key-Value 键值的方式来存储数据，其中 Key 和 Value 可以是简单的对象，也可以是复杂的对象。Key 作为唯一的标识符，优点是查找速度快，在这方面明显优于关系型数据库，缺点是无法像关系型数据库一样使用条件过滤（比如 WHERE），如果你不知道去哪里找数据，就要遍历所有的键，这就会消耗大量的计算。

键值型数据库典型的使用场景是作为`内存缓存`。`Redis `是最流行的键值型数据库。

<img src="https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/mysql_imgs/image-20211020172958427.png?raw=true" alt="image-20211020172958427" style="zoom:80%;" />

**文档型数据库**

此类数据库可存放并获取文档，可以是XML、JSON等格式。在数据库中文档作为处理信息的基本单位，一个文档就相当于一条记录。文档数据库所存放的文档，就相当于键值数据库所存放的“值”。MongoDB 是最流行的文档型数据库。此外，还有CouchDB等。

**搜索引擎数据库**

虽然关系型数据库采用了索引提升检索效率，但是针对全文索引效率却较低。搜索引擎数据库是应用在搜索引擎领域的数据存储形式，由于搜索引擎会爬取大量的数据，并以特定的格式进行存储，这样在检索的时候才能保证性能最优。核心原理是“倒排索引”。

典型产品：Solr、Elasticsearch、Splunk 等。

**列式数据库**

列式数据库是相对于行式存储的数据库，Oracle、MySQL、SQL Server 等数据库都是采用的行式存储（Row-based），而列式数据库是将数据按照列存储到数据库中，这样做的好处是可以大量降低系统的 I/O，适合于分布式文件系统，不足在于功能相对有限。典型产品：HBase等。

<img src="https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/mysql_imgs/image-20211020173921726.png?raw=true" alt="image-20211020173921726" style="zoom:90%;" />

**图形数据库**

图形数据库，利用了图这种数据结构存储了实体（对象）之间的关系。图形数据库最典型的例子就是社交网络中人与人的关系，数据模型主要是以节点和边（关系）来实现，特点在于能高效地解决复杂的关系问题。

图形数据库顾名思义，就是一种存储图形关系的数据库。它利用了图这种数据结构存储了实体（对象）之间的关系。关系型数据用于存储明确关系的数据，但对于复杂关系的数据存储却有些力不从心。如社交网络中人物之间的关系，如果用关系型数据库则非常复杂，用图形数据库将非常简单。典型产品：Neo4J、InfoGrid等。

<img src="https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/mysql_imgs/image-20211020180934455.png?raw=true" alt="image-20211020180934455.png" style="zoom:80%;" />

### 4. 关系型数据库设计规则

- 关系型数据库的典型数据结构就是`数据表`，这些数据表的组成都是结构化的（Structured）。

- 将数据放到表中，表再放到库中。

- 一个数据库中可以有多个表，每个表都有一个名字，用来标识自己。表名具有唯一性。

- 表具有一些特性，这些特性定义了数据在表中如何存储，类似Java和Python中 “类”的设计。

#### 4.1 表、记录、字段

- E-R（entity-relationship，实体-联系）模型中有三个主要概念是：`实体集`、`属性`、`联系集`。

- 一个实体集（class）对应于数据库中的一个表（table），一个实体（instance）则对应于数据库表中的一行（row），也称为一条记录（record）。一个属性（attribute）对应于数据库表中的一列（column），也称为一个字段（field）。

<img src="https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/mysql_imgs/image-20210914235450032-1634141235163.png?raw=true" alt="image-20210914235450032-1634141235163.png" style="zoom:80%;" />

```
ORM思想 (Object Relational Mapping)体现：
数据库中的一个表  <---> Java或Python中的一个类
表中的一条数据  <---> 类中的一个对象（或实体）
表中的一个列  <----> 类中的一个字段、属性(field)
```

#### 4.2 表的关联关系

- 表与表之间的数据记录有关系(relationship)。现实世界中的各种实体以及实体之间的各种联系均用关系模型来表示。

- 四种：一对一关联、一对多关联、多对多关联、自我引用

##### 4.2.1 一对一关联（one-to-one）

- 在实际的开发中应用不多，因为一对一可以创建成一张表。
- 举例：设计`学生表`：学号、姓名、手机号码、班级、系别、身份证号码、家庭住址、籍贯、紧急联系人、...
  - 拆为两个表：两个表的记录是一一对应关系。

  - `基础信息表`（常用信息）：学号、姓名、手机号码、班级、系别
  - `档案信息表`（不常用信息）：学号、身份证号码、家庭住址、籍贯、紧急联系人、...
- 两种建表原则： 
  - 外键唯一：主表的主键和从表的外键（唯一），形成主外键关系，外键唯一。 
  - 外键是主键：主表的主键和从表的主键，形成主外键关系。

<img src="https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/mysql_imgs/image-20210914235534452.png?raw=true" alt="image-20210914235534452.png" style="zoom:80%;" />

##### 4.2.2 一对多关系（one-to-many）

- 常见实例场景：`客户表和订单表`，`分类表和商品表`，`部门表和员工表`。
- 举例：
  - 员工表：编号、姓名、...、所属部门

  - 部门表：编号、名称、简介
- 一对多建表原则：在从表(多方)创建一个字段，字段作为外键指向主表(一方)的主键

<img src="https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/mysql_imgs/image-20210915001013524.png?raw=true" alt="image-20210915001013524.png" style="zoom:100%;" />

<img src="https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/mysql_imgs/image-20210914235610597.png?raw=true" alt="image-20210914235610597.png" style="zoom:70%;" />

<img src="https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/mysql_imgs/image-20210915084623432.png?raw=true" alt="image-20210915084623432.png" style="zoom:100%;" />


##### 4.2.3 多对多（many-to-many）

要表示多对多关系，必须创建第三个表，该表通常称为`联接表`，它将多对多关系划分为两个一对多关系。将这两个表的主键都插入到第三个表中。

<img src="https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/mysql_imgs/image-20210915001048215.png?raw=true" alt="image-20210915001048215.png" style="zoom:100%;" />

- **举例1：学生-课程**

  - `学生信息表`：一行代表一个学生的信息（学号、姓名、手机号码、班级、系别...）

  - `课程信息表`：一行代表一个课程的信息（课程编号、授课老师、简介...）

  - `选课信息表`：一个学生可以选多门课，一门课可以被多个学生选择

    ```
    学号     课程编号  
    1        1001
    2        1001
    1        1002
    ```

- **举例2：产品-订单**

  “订单”表和“产品”表有一种多对多的关系，这种关系是通过与“订单明细”表建立两个一对多关系来定义的。一个订单可以有多个产品，每个产品可以出现在多个订单中。

  - `产品表`：“产品”表中的每条记录表示一个产品。
  - `订单表`：“订单”表中的每条记录表示一个订单。
  - `订单明细表`：每个产品可以与“订单”表中的多条记录对应，即出现在多个订单中。一个订单可以与“产品”表中的多条记录对应，即包含多个产品。

<img src="https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/mysql_imgs/image-20210914235637068.png?raw=true" alt="image-20210914235637068.png" style="zoom:80%;" />

##### 4.2.4 自我引用(Self reference)

<img src="https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/mysql_imgs/image-20210914235651997.png?raw=true" alt="image-20210914235651997.png" style="zoom:70%;" />


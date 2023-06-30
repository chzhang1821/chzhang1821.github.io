---
layout:     post
title:      "设计模式的七大原则"
subtitle:   
date:       2023-06-27 16:00
author:     "Chenghao"
header-img: "img/post-bg-2015.jpg"
tags:
    - 设计模式
---

> 笔记摘自：尚硅谷 | B站视频链接：<https://www.bilibili.com/video/BV1G4411c7N4/>

# 设计模式七大原则

## 1. 设计模式的目的

编写软件过程中，程序员面临着**耦合性、内聚性**以及**可维护性、可扩展性、冲永兴、灵活性**等多方面挑战，设计模式是为了让程序具有更好的

1. 代码重用性（即：相同功能的代码，不用多次编写）
2. 可读性（即：编程规范性，便于其他程序员的阅读和理解）
3. 可扩展性（即：当需要增加新的功能时，非常的方便，称为可维护性）
4. 可靠性（即：当我们增加新的功能后，对原来的功能没有影响）
5. 使程序呈现高内聚（模块内部紧密），低耦合（各模块之间依赖性弱，影响弱）的特性



## 2. 设计模式七大原则

设计模式原则，其实就是程序员在编程时，应当遵守的原则，也是各种设计模式的基础（即：设计模式为什么 这样设计的依据）

设计模式常用的七大原则：

1. 单一职责原则
2. 接口隔离原则
3. 依赖倒转（倒置）原则
4. 里氏替换原则
5. 开闭原则
6. 迪米特法则
7. 合成复用原则



## 3. 单一职责原则 (Single Responsibility Principle)

### 3.1 基本介绍

对类来说的，即一个类应该只负责一项职责。如类 A 负责两个不同职责：职责 1，职责 2。当职责 1 需求变更 而改变 A 时，可能造成职责 2 执行错误，所以需要将类 A 的粒度分解为 A1，A2

例如在web项目中的dao层，若`dao`类同时负责`user`表和`order`表的增删改查，则应当将`dao`类分为`userDao`和`orderDao`

### 3.2 应用实例

以交通供给案例讲解

1. 方案1

```java
public class SingleResponsibility1 {
    public static void main(String[] args) {
        // TODO: Auto-generated method stub
        Vehicle vehicle = new Vehicle();
        vehicle.run("摩托车");
        vehicle.run("汽车");
        vehicle.run("飞机");
    }
}

// 交通工具类
// 方式1
// 1. 在方式1 的 run 方法中，违反了单一职责原则，（飞机和船不能在公路上跑）
// 2. 解决的方案非常简单，根据交通工具运行方法不同，分解成不同类
class Vehicle {
    public void run(String vehicle) {
        System.out.println(vehicle + " 在公路上运行 ...");
    }
}
```

2. 方案2

```java
public class SingleResponsibility2 {
    public static void main(String[] args) {
        // TODO: Auto-generated method stub
        RoadVehicle roadVehicle = new RoadVehicle();
        roadVehicle.run("摩托车");
        roadVehicle.run("汽车");

        AirVehicle airVehicle = new AirVehicle();
        airVehicle.run("飞机");
    }
}

// 方案2的分析
// 1. 遵守了单一职责原则
// 2. 但这样做改动很大，即将类分解，同时需要修改客户端
// 3. 改进：直接修改 Vehicle 类，改动的代码会比较少 ==> 方案3
class RoadVehicle {
    public void run(String vehicle) {
        System.out.println(vehicle + " 在公路上运行 ...");
    }
}

class AirVehicle {
    public void run(String vehicle) {
        System.out.println(vehicle + " 在天空中运行 ...");
    }
}

class WaterVehicle {
    public void run(String vehicle) {
        System.out.println(vehicle + " 在水中运行 ...");
    }
}
```

3. 方案3

```java
public class SingleResponsibility3 {
    public static void main(String[] args) {
        // TODO: Auto-generated method stub
        Vehicle2 vehicle2 = new Vehicle2();
        vehicle2.run("汽车");
        vehicle2.runAir("飞机");
        vehicle2.runWater("轮船");
    }
}

// 方式3的分析
// 1. 这种修改方法没有对原来的类做大的修改，只是增加了方法
// 2. 虽然在类的级别上没有遵守单一职责原则，但在方法级别上，仍然是遵守了单一职责原则
class Vehicle2 {
    public void run(String vehicle) {
        System.out.println(vehicle + " 在公路上运行 ...");
    }

    public void runAir(String vehicle) {
        System.out.println(vehicle + " 在天空中上运行 ...");
    }

    public void runWater(String vehicle) {
        System.out.println(vehicle + " 在水中运行 ...");
    }
}
```



### 3.3 单一职责原则注意事项和细节

1. 降低类的复杂度，一个类只负责一项职责
2. 提高类的可读性，可维护性
3. 降低变更引起的风险 -- 以方案2为例，若要更改在陆地上跑的方法，则只需要修改`RoadVehicle`类，而不会影响到其他类，方案3中的方法也类似
4. 通常情况下，我们应当遵守单一职责原则，只有逻辑足够简单，才可以在代码上违反单一职责原则；只有类中方法数量足够少，可以在方法级别包吃单一职责原则 - 例如方案3，如果方法过多，且方法和运行的方式有关，则仍然需要拆分成多个类

> 减少使用`if ... else if ... else ...`，这种写法会使得代码耦合性太高，针对不同的职责，可以创建不同的类来化解多分支



## 4. 接口隔离原则 (Interface Segregation Principle)

### 4.1 基本介绍

1. 客户端不应该依赖它不需要的接口，**即一个类对另一个类的依赖应该建立在最小的接口上**（一个类对另一个类的依赖可能是介于一个接口）

2. <img src="/Users/chenghaozhang/Library/CloudStorage/OneDrive-Personal/MyBlog/chzhang1821.github.io/img/design_pattern_imgs/接口隔离原则.png" alt="接口隔离原则" style="zoom:50%"/>

> B和D实现了Interface1，B和D需要实现Interface1中所有的方法

3. 类 A 通过接口 Interface1 依赖类 B，类 C 通过接口 Interface1 依赖类 D，B和D都需要实现不被需要的方法，如果接口 Interface1 对于类 A 和类 C 来说不是最小接口，那么类 B 和类 D 必须去实现他们不需要的方法。
4. 按隔离原则应当这样处理：

 将接口 Interface1 拆分为独立的几个接口(这里我们拆分成 3 个接口)，类 A 和类 C 分别与他们需要的接口建立 依赖关系。也就是采用接口隔离原则

### 4.2 应用实例

```java
public class InterfaceSegregation1 {
      public static void main(String[] args) {
          A a = new A();
          a.depend1(new B()); // B 实现了 operation1
          a.depend2(new B()); // B 实现了 operation2
          a.depend3(new B()); // B 实现了 operation3

          C c = new C(); 
          c.depend1(new D()); // D 实现了 operation1
          c.depend4(new D()); // D 实现了 operation4
          c.depend5(new D()); // D 实现了 operation5
    }
}

interface Interface1 {
    void operation1();
    void operation2();
    void operation3();
    void operation4();
    void operation5();
}

class B implements Interface1 {

    @Override
    public void operation1() {
        System.out.println("B 实现了 operation1");
    }

    @Override
    public void operation2() {
        System.out.println("B 实现了 operation2");
    }

    @Override
    public void operation3() {
        System.out.println("B 实现了 operation3");
    }

    @Override
    public void operation4() {
        System.out.println("B 实现了 operation4");
    }

    @Override
    public void operation5() {
        System.out.println("B 实现了 operation5");
    }
}

class D implements Interface1 {
    @Override
    public void operation1() {
        System.out.println("D 实现了 operation1");
    }

    @Override
    public void operation2() {
        System.out.println("D 实现了 operation2");
    }

    @Override
    public void operation3() {
        System.out.println("D 实现了 operation3");
    }

    @Override
    public void operation4() {
        System.out.println("D 实现了 operation4");
    }

    @Override
    public void operation5() {
        System.out.println("D 实现了 operation5");
    }
}

class A { // A 类通过接口 Interface1 依赖（使用）B 类，但只会用到 operation 1 2 3
    public void depend1(Interface1 i) {
        i.operation1();
    }
    public void depend2(Interface1 i) {
        i.operation2();
    }
    public void depend3(Interface1 i) {
        i.operation3();
    }
}

class C { // C 类通过接口 Interface1 依赖（使用）D 类，但只会用到 operation 1 4 5
    public void depend1(Interface1 i) {
        i.operation1();
    }
    public void depend4(Interface1 i) {
        i.operation4();
    }
    public void depend5(Interface1 i) {
        i.operation5();
    }
}
```



### 4.3 传统方法的问题和使用接口隔离原则改进

1) 类 A 通过接口 Interface1 依赖类 B， 类 C 通过接口 Interface1 依赖类 D， 如果接口 Interface1 对于类 A 和类 C 来说不是最小接口，那么类 B 和类 D 必须去实现他们不需要的方法

2) 将接口 Interface1 拆分为独立的几个接口，类 A 和类 C 分别与他们需要的接口建立依赖关系。也就是采用接口 隔离原则

3) 接口 Interface1 中出现的方法，根据实际情况拆分为三个接口

<img src="/Users/chenghaozhang/Library/CloudStorage/OneDrive-Personal/MyBlog/chzhang1821.github.io/img/design_pattern_imgs/接口隔离原则2.png" alt="接口隔离原则2" style="zoom:60%"/>

4. 代码实现

```java
package com.chenghao;

public class InterfaceSegregation1 {
    public static void main(String[] args) {
        A a = new A();
        a.depend1(new B()); // A 通过接口依赖 B // B 实现了 operation1
        a.depend2(new B()); // B 实现了 operation2
        a.depend3(new B()); // B 实现了 operation3

        C c = new C();
        c.depend1(new D()); // C 通过接口依赖 D // D 实现了 operation1
        c.depend4(new D()); // D 实现了 operation4
        c.depend5(new D()); // D 实现了 operation5
    }
}

interface Interface1 {
    void operation1();

}

interface Interface2 {
    void operation2();
    void operation3();
}

interface Interface3 {
    void operation4();
    void operation5();
}

class B implements Interface1, Interface2 {

    @Override
    public void operation1() {
        System.out.println("B 实现了 operation1");
    }

    @Override
    public void operation2() {
        System.out.println("B 实现了 operation2");
    }

    @Override
    public void operation3() {
        System.out.println("B 实现了 operation3");
    }

}

class D implements Interface1, Interface3 {
    @Override
    public void operation1() {
        System.out.println("D 实现了 operation1");
    }

    @Override
    public void operation4() {
        System.out.println("D 实现了 operation4");
    }

    @Override
    public void operation5() {
        System.out.println("D 实现了 operation5");
    }
}

class A { // A 类通过接口 Interface1 依赖（使用）B 类，但只会用到 operation 1 2 3
    public void depend1(Interface1 i) {
        i.operation1();
    }
    public void depend2(Interface2 i) {
        i.operation2();
    }
    public void depend3(Interface2 i) {
        i.operation3();
    }
}

class C {
    public void depend1(Interface1 i) {
        i.operation1();
    }
    public void depend4(Interface3 i) {
        i.operation4();
    }
    public void depend5(Interface3 i) {
        i.operation5();
    }
}

```



## 5. 依赖倒转原则

### 5.1 基本介绍

依赖倒转原则(Dependence Inversion Principle)是指：

1) 高层模块不应该依赖低层模块，二者都应该依赖其抽象

2) 抽象不应该依赖细节，细节应该依赖抽象

3) 依赖倒转(倒置)的中心思想是面向接口编程

4) 依赖倒转原则是基于这样的设计理念：相对于细节的多变性，抽象的东西要稳定的多。以抽象为基础搭建的架 构比以细节为基础的架构要稳定的多。在 java 中，抽象指的是接口或抽象类，细节就是具体的实现类

5) 使用接口或抽象类的目的是**制定好规范**（价值在于设计），而不涉及任何具体的操作，把展现细节的任务交给他们的实现类去完 成。

### 5.2 应用实例

1. 实现方案1

```java
public class DependencyInversion {
    public static void main(String[] args) {
        Person person = new Person();
        person.receive(new Email());
    }
}

class Email {
    public String getInfo() {
        return "电子邮件信息：hello, world";
    }
}

// 完成Person接收消息的功能
// 方式1分析
// 1. 简单，比较容易想到
// 2. 如果我们获取的对象是微信、短信等等，则需要新增类，同时Person也要增加相应的接收方法
// 3. 解决思路：引入一个抽象的接口 Ireceiver， 表示接受者，这样Person类与该接口发生依赖
//            因为 Email、微信等等都属于接收者的范围，他们各自实现Ireciever这个接口，这样就符合依赖倒转原则
class Person {
    public void receive(Email email) { // 这里直接写了一个类，
        System.out.println(email.getInfo());
    }
}
```



2. 实现方案2

```java
public class DependencyInversion2 {
    public static void main(String[] args) {
        Person2 person = new Person2();
        person.receive(new Email2());
        person.receive(new WeChat());
    }
}

interface IReceiver {
    public String getInfo();
}

class Email2 implements IReceiver {
    @Override
    public String getInfo() {
        return "电子邮件信息：hello, world";
    }
}

class WeChat implements IReceiver {
    @Override
    public String getInfo() {
        return "微信信息：hello, ok";
    }
}

// 方式2分析
class Person2 {
    // 这里依赖于接口，稳定性好
    public void receive(IReceiver receiver) {
        System.out.println(receiver.getInfo());
    }
}
```

### 5.3 依赖关系传递的三种方式和应用案例

1) 接口传递

2) 构造方法传递

3) setter 方式传递

```java
package com.chenghao;

public class DependencyPass {
    public static void main(String[] args) {
        // 方式1 -- 接口
        ITV changHong = new ChangHong();
        IOpenAndClose openAndClose = new OpenAndClose();
        openAndClose.open(changHong);

        // 方式2 -- 构造器
        IOpenAndClose openAndClose = new OpenAndClose(new ChangHong());
        openAndClose.open();

        // 方式3 -- setter
        IOpenAndClose openAndClose = new OpenAndClose();
        openAndClose.setTv(new ChangHong());
        openAndClose.open();
    }
}

class ChangHong implements ITV {
    @Override
    public void play() {
        System.out.println("长虹电视机，打开");
    }
}

// 方式1：通过接口传递实现依赖
// ITV 接口
interface ITV {
    public void play();
}

interface IOpenAndClose {
    public void open(ITV tv); // 抽象方法，接收接口
}

// 实现接口
class OpenAndClose implements IOpenAndClose {
    @Override
    public void open(ITV tv) {
        tv.play();
    }
}

// 方式2：通过构造方法依赖传递
interface ITV {
    public void play();
}

interface IOpenAndClose {
    public void open(); // 抽象方法，接收接口
}

// 实现接口
class OpenAndClose implements IOpenAndClose {
    public ITV tv; // 成员
    public OpenAndClose(ITV tv) {
        this.tv = tv;
    }
    @Override
    public void open() {
        tv.play();
    }
}

// 方式3， 通过setter方法传递
interface ITV {
    public void play();
}

interface IOpenAndClose {
    public void open(); // 抽象方法，接收接口
    public void setTv(ITV tv);
}

// 实现接口
class OpenAndClose implements IOpenAndClose {
    public ITV tv; // 成员

    @Override
    public void open() {
        tv.play();
    }

    @Override
    public void setTv(ITV tv) {
        this.tv = tv;
    }
}
```



### 5.4 依赖倒转原则的注意事项和细节

1) 低层模块尽量都要有抽象类或接口，或者两者都有，程序稳定性更好.

2) 变量的声明类型尽量是抽象类或接口, 这样我们的变量引用和实际对象间，就存在一个缓冲层，利于程序扩展 和优化

3) 继承时遵循里氏替换原则






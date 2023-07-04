---
layout:     post
title:      "数据结构与算法 04-栈"
subtitle:   
date:       2023-06-30 16:00
author:     "Chenghao"
header-img: "img/post-bg-2015.jpg"
tags:
    - 数据结构与算法
    - Java
---

> 笔记摘自：尚硅谷 | B站视频链接：<https://www.bilibili.com/video/BV1E4411H73v/>

# 栈

## 1. 栈的一个实际需求

请输入一个表达式

计算式：$7 \times 2 \times 2 - 5 + 1 - 5 + 3 - 3$ 点击计算

<img src = "https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/data_structure_imgs/栈01.png?raw=true" alt = "栈01" style = "zoom: 50%" />

请问: 计算机底层是如何运算得到结果的？ 注意不是简单的把算式列出运算,因为我们看这个算式 7 * 2 * 2 5, 但是计算机怎么理解这个算式的(对计算机而言，它接收到的就是一个字符串)，我们讨论的是这个问题。-> 栈

## 2. 栈的介绍

1) 栈的英文为(stack)

2) 栈是一个先入后出(FILO-First In Last Out)的有序列表。

3) 栈(stack)是限制线性表中元素的插入和删除只能在线性表的同一端进行的一种特殊线性表。允许插入和删除的 一端，为变化的一端，称为栈顶(Top)，另一端为固定的一端，称为栈底(Bottom)。

4) 根据栈的定义可知，最先放入栈中元素在栈底，最后放入的元素在栈顶，而删除元素刚好相反，最后放入的元 素最先删除，最先放入的元素最后删除

5) 图解方式说明出栈(pop)和入栈(push)的概念

<img src = "https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/data_structure_imgs/栈02.png?raw=true" alt = "栈02" style = "zoom: 50%" />

## 3. 栈的应用场景

1) 子程序的调用：在跳往子程序前，会先将下个指令的地址存到堆栈中，直到子程序执行完后再将地址取出，以 回到原来的程序中。

2) 处理递归调用：和子程序的调用类似，只是除了储存下一个指令的地址外，也将参数、区域变量等数据存入堆 栈中。

3) 表达式的转换[中缀表达式转后缀表达式]与求值(实际解决)。

4) 二叉树的遍历。

5) 图形的深度优先(depth 一 first)搜索法。



## 4. 栈的快速入门

1) 用数组模拟栈的使用，由于栈是一种有序列表，当然可以使用数组的结构来储存栈的数据内容， 下面我们就用数组模拟栈的出栈，入栈等操作。

2) 实现思路分析,并画出示意图

<img src = "https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/data_structure_imgs/栈03.png?raw=true" alt = "栈03" style = "zoom: 50%" />

3. 代码实现

* 数组模拟栈

```java

/**
 * 数组模拟栈
 */
public class ArrayStackDemo {
    public static void main(String[] args) {
        //测试一下 ArrayStack 是否正确
        // 先创建一个 ArrayStack 对象->表示栈
        ArrayStack stack = new ArrayStack(4);
        String key = "";
        boolean loop = true; //控制是否退出菜单
        Scanner scanner = new Scanner(System.in);

        while (loop) {
            System.out.println("show: 表示显示栈");
            System.out.println("exit: 退出程序");
            System.out.println("push: 表示添加数据到栈(入栈)");
            System.out.println("pop: 表示从栈取出数据(出栈)");
            System.out.println("请输入你的选择");
            key = scanner.next();

            switch (key) {
                case "show":
                    stack.list();
                    break;
                case "push":
                    System.out.println("请输入一个数");
                    int value = scanner.nextInt();
                    stack.push(value);
                    break;
                case "pop":
                    try {
                        int res = stack.pop();
                        System.out.printf("出栈的数据是 %d \n", res);
                    } catch (Exception e) {
                        System.out.println(e.getMessage());
                    }
                    break;
                case "exit":
                    scanner.close();
                    loop = false;
                    break;
                default:
                    break;
            }
        }
        System.out.println("程序退出");
    }
}

class ArrayStack {
    private int maxSize; // 栈的大小
    private int[] stack; // 数组，模拟栈
    private int top = -1;

    public ArrayStack(int maxSize) {
        this.maxSize = maxSize;
        this.stack = new int[this.maxSize];
    }

    public boolean isFull() {
        return top == maxSize - 1;
    }

    public boolean isEmpty() {
        return top == -1;
    }

    public void push(int num) {
        if (isFull()) {
            System.out.println("栈满");
            return;
        }
        top++;
        stack[top] = num;
    }

    public int pop() {
        if (isEmpty()) {
            throw new RuntimeException("栈空");
        }
        int value = stack[top];
        top--;
        return value;
    }

    public void list() {
        if (isEmpty()) {
            System.out.println("栈空");
            return;
        }
        for (int i = top; i >= 0; i--) {
            System.out.printf("stack[%d] = %d\n", i, stack[i]);
        }
    }
}
```

* 链表模拟栈

```java

/**
 * 链表模拟栈
 */
public class LinkedListStackDemo {
    public static void main(String[] args) {
        //测试一下 ArrayStack 是否正确
        // 先创建一个 ArrayStack 对象->表示栈
        LinkedListStack stack = new LinkedListStack(4);
        String key = "";
        boolean loop = true; //控制是否退出菜单
        Scanner scanner = new Scanner(System.in);

        while (loop) {
            System.out.println("show: 表示显示栈");
            System.out.println("exit: 退出程序");
            System.out.println("push: 表示添加数据到栈(入栈)");
            System.out.println("pop: 表示从栈取出数据(出栈)");
            System.out.println("请输入你的选择");
            key = scanner.next();

            switch (key) {
                case "show":
                    stack.list();
                    break;
                case "push":
                    System.out.println("请输入一个数");
                    int value = scanner.nextInt();
                    stack.push(value);
                    break;
                case "pop":
                    try {
                        int res = stack.pop();
                        System.out.printf("出栈的数据是 %d \n", res);
                    } catch (Exception e) {
                        System.out.println(e.getMessage());
                    }
                    break;
                case "exit":
                    scanner.close();
                    loop = false;
                    break;
                default:
                    break;
            }
        }
        System.out.println("程序退出");
    }
}

class ListNode {
    int value;
    ListNode next;

    public ListNode(int value) {
        this.value = value;
    }
}
class LinkedListStack {
    ListNode head = new ListNode(-1);
    private int maxSize;
    private ListNode top = head.next;

    public LinkedListStack(int maxSize) {
        this.maxSize = maxSize;
    }

    public boolean isEmpty() {
        if (head.next == null) {
            return true;
        }
        return false;
    }
    public boolean isFull() {
        if (head.next == null) {
            return false;
        }
        int count = 0;
        ListNode node = top;
        while (node != null) {
            count++;
            node = node.next;
        }
        if (count == maxSize) {
            return true;
        }
        return false;
    }

    public void push(int value) {
        if (isFull()) {
            System.out.println("栈满");
            return;
        }
        ListNode node = new ListNode(value);
//        top = head.next;
        head.next = node;
        node.next = top;
        top = node;
    }
    public int pop() {
        if (isEmpty()) {
            throw new RuntimeException("栈空");
        }
        int value = top.value;
        top = top.next;
        head.next = top;
        return value;
    }

    public void list() {
        if (isEmpty()) {
            System.out.println("栈空");
            return;
        }
        ListNode temp = top;
        while (temp != null) {
            System.out.printf("stack: %d \n", temp.value);
            temp = temp.next;
        }
    }
}
```



## 5. 栈实现综合计算器(中缀表达式)

<img src = "https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/data_structure_imgs/栈04.png?raw=true" alt = "栈04" style = "zoom: 50%" />

代码实现

```java
public class Calculator {
    public static void main(String[] args) {
        String expression = "7+2*6-4";
        // 创建两个栈
        ArrayStack2 numberStack = new ArrayStack2(10);
        ArrayStack2 operStack = new ArrayStack2(10);

        // 定义需要的相关变量
        int index = 0; // 扫描表达式的指针
        int num1 = 0;
        int num2 = 0;
        int oper = 0;
        int res = 0;
        char ch = ' '; // 将每次扫描得到的char保存到ch
        String keepNum = ""; // 用于拼接多位数
        // 开始扫描
        while (true) {
            // 一次得到expression中的每一个字符
            ch = expression.substring(index, index + 1).charAt(0);
            // 判断ch是什么，做相应处理
            if (operStack.isOper(ch)) { // 如果是运算符
                // 判断符号栈是否为空
                if (!operStack.isEmpty()) {
                    // 符号优先级小
                    if (operStack.priority(ch) <= operStack.priority(operStack.peak())) {
                        num1 = numberStack.pop();
                        num2 = numberStack.pop();
                        oper = operStack.pop();
                        res = numberStack.cal(num1, num2, oper);
                        numberStack.push(res);
                        operStack.push(ch);
                    } else {
                        // 符号优先级比top的大，则push
                        operStack.push(ch);
                    }
                } else {
                    // 如果符号栈为空，直接push
                    operStack.push(ch);
                }
            } else { // 如果是数字，则直接push
//                numberStack.push(ch - '0');
                // 处理多位数时，不能发现一位数就立即入栈，可能是多位数
                // 在处理数时，向后再看一位，如果是书就继续扫描，如果是符号才入栈
                // 定义一个字符串变量用于拼接

                // 处理多位数
                keepNum += ch;
                // 如果ch已经是最后一位，直接入栈
                if (index == expression.length() - 1) {
                    numberStack.push(ch - '0');
                } else {
                    // 判断下一位是不是数字，如果是则继续扫描，如果是运算符就入栈
                    if (operStack.isOper(expression.substring(index + 1, index + 2).charAt(0))) {
                        numberStack.push(Integer.parseInt(keepNum));
                        keepNum = "";
                    }
                }
            }
            // 让index + 1，并判断是否扫描到expression最后
            index++;
            if (index >= expression.length()) {
                break;
            }
        }
        while (true) {
            if (operStack.isEmpty()) {
                break;
            } else {
                num1 = numberStack.pop();
                num2 = numberStack.pop();
                oper = operStack.pop();
                res = numberStack.cal(num1, num2, oper);
                numberStack.push(res);
            }
        }
        System.out.printf("表达式 %s = %d \n", expression, numberStack.pop());
    }
}


class ArrayStack2 {
    private int maxSize; // 栈的大小
    private int[] stack; // 数组，模拟栈
    private int top = -1;

    public ArrayStack2(int maxSize) {
        this.maxSize = maxSize;
        this.stack = new int[this.maxSize];
    }

    public boolean isFull() {
        return top == maxSize - 1;
    }

    public boolean isEmpty() {
        return top == -1;
    }

    public void push(int num) {
        if (isFull()) {
            System.out.println("栈满");
            return;
        }
        top++;
        stack[top] = num;
    }

    public int pop() {
        if (isEmpty()) {
            throw new RuntimeException("栈空");
        }
        int value = stack[top];
        top--;
        return value;
    }

    public void list() {
        if (isEmpty()) {
            System.out.println("栈空");
            return;
        }
        for (int i = top; i >= 0; i--) {
            System.out.printf("stack[%d] = %d\n", i, stack[i]);
        }
    }

    // 返回运算符的优先级，优先级是程序员来确定，优先级使用数字表示
    // 数字越大，则优先级就越高
    public int priority(int oper) {
        if (oper == '*' || oper == '/') {
            return 1;
        } else if (oper == '+' || oper == '-') {
            return 0;
        } else {
            return -1; // 假定目前的表达式只有 +, -, *, /
        }
    }

    public boolean isOper(char val) {
        return val == '+' || val == '-' || val == '*' || val == '/';
    }

    public int cal(int num1, int num2, int oper) {
        int res = 0;
        switch (oper) {
            case '+':
                res = num1 + num2;
                break;
            case '-':
                res = num2 - num1; // 注意顺序
                break;
            case '*':
                res = num2 * num1;
                break;
            case '/':
                res = num2 / num1; // 注意顺序
                break;
            default:
                break;
        }
        return res;
    }

    // 返回当前栈顶的一个值，不是真正的pop
    public int peak() {
        return stack[top];
    }
}
```

> 如果出现连减的情况，则该计算器失效



## 6. 逆波兰计算器

我们完成一个逆波兰计算器，要求完成如下任务:

1) 输入一个逆波兰表达式(后缀表达式)，使用栈(Stack), 计算其结果
   1) 后缀表达式要求**所有符号都是要在运算数字的后面出现**

2) 支持小括号和多位数整数，因为这里我们主要讲的是数据结构，因此计算器进行简化，只支持对整数的计算。

3) 思路分析

> 例如: (3+4)×5-6 对应的后缀表达式就是 3 4 + 5 × 6 - , 针对后缀表达式求值步骤如下:
>
> 1． 从左至右扫描，将 3 和 4 压入堆栈； 
>
> 2． 遇到+运算符，因此弹出 4 和 3（4 为栈顶元素， 3 为次顶元素），计算出 3+4 的值，得 7， 再将 7 入栈； 
>
> 3． 将 5 入栈； 
>
> 4． 接下来是×运算符，因此弹出 5 和 7， 计算出 7×5=35， 将 35 入栈； 
>
> 5． 将 6 入栈； 
>
> 6． 最后是-运算符，计算出 35-6 的值，即 29， 由此得出最终结果

代码实现：

```java
package com.chenghao;

import java.util.ArrayList;
import java.util.List;
import java.util.Stack;

public class PolandNotation {
    public static void main(String[] args) {
        // 定义逆波兰表达式
        // 4 * 5 - 8 + 60 + 8 / 2
        String suffixExpression = "4 5 * 8 - 60 + 8 2 / +";
        List<String> list = getListString(suffixExpression);

        System.out.println(list);

        int res = calculate(list);
        System.out.println("计算结果是 = " + res);

    }

    public static List<String> getListString(String suffixExpression) {
        // 将 suffixExpression 分割
        String[] split = suffixExpression.split(" ");
        List<String> list = new ArrayList<>();
        for (String s : split) {
            list.add(s);
        }
        return list;
    }

    public static int calculate(List<String> ls) {
        Stack<String> stack = new Stack<>();
        // 遍历 ls
        for (String item : ls) {
            if (item.matches("\\d+")) { // 匹配多位数
                stack.push(item);
            } else {
                int num2 = Integer.parseInt(stack.pop());
                int num1 = Integer.parseInt(stack.pop());
                int res = 0;
                if (item.equals("+")) {
                    res = num1 + num2;
                } else if (item.equals("-")) {
                    res = num1 - num2;
                } else if (item.equals("*")) {
                    res = num1 * num2;
                } else if (item.equals("/")) {
                    res = num1 / num2;
                } else {
                    throw new RuntimeException("运算符有误");
                }
                stack.push(String.valueOf(res));
            }
        }
        return Integer.parseInt(stack.pop());
    }
}
```



## 7. 中缀表达式转换为后缀表达式

大家看到，后缀表达式适合计算式进行运算，但是人却不太容易写出来，尤其是表达式很长的情况下，因此在开发 中，我们需要将 中缀表达式转成后缀表达式。

### 7.1 具体步骤如下

1. 初始化两个栈：运算符栈 s1 和储存中间结果的栈 s2；

2) 从左至右扫描中缀表达式；

3) 遇到操作数时，将其压 s2；

4) 遇到运算符时，比较其与 s1 栈顶运算符的优先级：
   1) 如果 s1 为空，或栈顶运算符为左括号“(”，则直接将此运算符入栈；

   2) 否则， 若优先级比栈顶运算符的高，也将运算符压入 s1；

   3) 否则， 将 s1 栈顶的运算符弹出并压入到 s2 中，再次转到(4-1)与 s1 中新的栈顶运算符相比较；

5) 遇到括号时：
   1) 如果是左括号“(”，则直接压入 s1

   2) 如果是右括号“)”，则依次弹出 s1 栈顶的运算符，并压入 s2， 直到遇到左括号为止，此时将这一对括号丢弃


6) 重复步骤 2 至 5， 直到表达式的最右边

7) 将 s1 中剩余的运算符依次弹出并压入 s2

8) 依次弹出 s2 中的元素并输出，结果的逆序即为中缀表达式对应的后缀表达式

<img src = "https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/data_structure_imgs/中缀转后缀.png?raw=true" alt = "中缀转后缀" style = "zoom: 50%" />

代码实现：

```java
package com.chenghao;

import java.util.ArrayList;
import java.util.List;
import java.util.Stack;

public class PolandNotation {
    public static void main(String[] args) {
//        // 定义逆波兰表达式
//        // 4 * 5 - 8 + 60 + 8 / 2
//        String suffixExpression = "4 5 * 8 - 60 + 8 2 / +";
//        List<String> list = getListString(suffixExpression);
//
//        System.out.println(list);
//
//        int res = calculate(list);
//        System.out.println("计算结果是 = " + res);

        String expression = "1+((2+3)*4)-5";
        List<String> infixExpressionList = toInfixExpressionList(expression);
        System.out.println(infixExpressionList); // [1, +, (, 2, +, 3, ), *, 4, ), -, 5]

        List<String> suffixExpressionList = toSuffixExpressionList(infixExpressionList);
        System.out.println(suffixExpressionList); // [1, 2, 3, +, 4, *, +, 5, -]

        System.out.printf("expression = %d \n", calculate(suffixExpressionList));

    }

    public static List<String> getListString(String suffixExpression) {
        // 中缀转后缀
        // 将 suffixExpression 分割
        String[] split = suffixExpression.split(" ");
        List<String> list = new ArrayList<>();
        for (String s : split) {
            list.add(s);
        }
        return list;
    }

    public static int calculate(List<String> ls) {
        Stack<String> stack = new Stack<>();
        // 遍历 ls
        for (String item : ls) {
            if (item.matches("\\d+")) { // 匹配多位数
                stack.push(item);
            } else {
                int num2 = Integer.parseInt(stack.pop());
                int num1 = Integer.parseInt(stack.pop());
                int res = 0;
                if (item.equals("+")) {
                    res = num1 + num2;
                } else if (item.equals("-")) {
                    res = num1 - num2;
                } else if (item.equals("*")) {
                    res = num1 * num2;
                } else if (item.equals("/")) {
                    res = num1 / num2;
                } else {
                    throw new RuntimeException("运算符有误");
                }
                stack.push(String.valueOf(res));
            }
        }
        return Integer.parseInt(stack.pop());
    }

    public static List<String> toInfixExpressionList(String s) {
        List<String> list = new ArrayList<>();
        int index = 0;
        String str; // 多位数拼接
        char c; // 没遍历到一个字符，放入到c
        do {
            // 如果是非数字，我需要加入到ls
            if ((c = s.charAt(index)) < 48 || (c = s.charAt(index)) > 57) {
                list.add("" + c);
                index++;
            } else {
                // 如果是一个数，则需要考虑多位数的问题
                str = "";
                while (index < s.length() && (c = s.charAt(index)) >= 48 && (c = s.charAt(index)) <= 57) {
                    str += c;
                    index++;
                }
                list.add(str);
            }
        } while (index < s.length());

        return list;
    }

    public static List<String> toSuffixExpressionList(List<String> infixExpressionList) {
        Stack<String> s1 = new Stack<>(); // 符号栈
        List<String> s2 = new ArrayList<>(); // 存储结果

        for (String item : infixExpressionList) {
            // 如果是一个数，则入s2
            if (item.matches("\\d+")) {
                s2.add(item);
            } else if (item.equals("(")) {
                s1.push(item);
            } else if (item.equals(")")) {
                while (!s1.peek().equals("(")) {
                    s2.add(s1.pop());
                }
                s1.pop(); // 将 ( 弹出栈
            } else {
                // 当item优先级小于等于栈顶运算符的优先级
                while (s1.size() != 0 && Operation.getValue(s1.peek()) >= Operation.getValue(item)) {
                    s2.add(s1.pop());
                }
                s1.push(item);
            }
        }

        while (s1.size() != 0) {
            s2.add(s1.pop());
        }

        return s2;

    }
}


/**
 * 返回运算符对应的优先级
 */
class Operation {
    private static int ADD = 1;
    private static int SUB = 1;
    private static int MUL = 2;
    private static int DIV = 2;

    public static int getValue(String operation) {
        int result = 0;
        switch (operation) {
            case "+" -> result = ADD;
            case "-" -> result = SUB;
            case "*" -> result = MUL;
            case "/" -> result = DIV;
            default -> System.out.println("不存在该运算符");
        }
        return result;
    }
}
```


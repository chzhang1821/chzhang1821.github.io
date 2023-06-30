---
layout:     post
title:      "项目实战--瑞吉外卖Day01"
subtitle:   
date:       2023-06-27 16:00
author:     "Chenghao"
header-img: "img/post-bg-2015.jpg"
tags:
    - Spring
    - Springboot
    - project
---

# 瑞吉外卖项目笔记


## 1. 软件开发整体介绍
### 1.1 软件开发流程

<img src="https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/ruiji_project_imgs/软件开发流程.png?raw=true" alt="软件开发流程" style="zoom:50%;" />

### 1.2 角色分工
- 项目经理：对整个项目负责，人物分配、把控进度
- 产品经理：进行需求调研，输出需求调研文档、产品原型等
- UI设计师：根据产品原型输出界面效果图
- 架构师：项目整体架构设计、技术选型等
- 开发工程师：代码实现
- 测试工程师：编写测试用例，输出测试报告
- 运维工程师：软件环境搭建、项目上线


### 1.3 软件环境
* 开发环境(development)：开发人员在开发阶段的环境，一般外部用户无法访问
* 测试环境(testing)：专门给测试人员使用的环境，用于测试项目，一般外部用户无法访问
* 生产环境(production)：即线上环境，正式提供对外服务的环境

## 2. 瑞吉外卖项目介绍
### 2.1 项目介绍
本项目（瑞吉外卖）是专门为餐饮企业（餐厅、饭店）定制的一款软件产品，包括系统管理后台和移动端应用两部分。其中系统管理后台主要提供给餐饮企业内部员工使用，可以对餐厅的菜品、套餐、订单等进行管理维护。移动端应用主要提供给消费者使用，可以在线浏览菜品、添加购物车、下单等。

本项目共分为3期进行开发：

第一期主要实现基本需求，其中移动端应用通过H5实现，用户可以通过手机浏览器访问

第二期主要针对移动端应用进行改进，使用微信小程序实现，用户使用起来更加方便

第三期主要针对系统进行优化升级，提高系统的访问性能


### 2.2 产品原型展示

**产品原型**，就是一款产品成型之前的一个简单的框架，就是将页面的排版布局展现出来，是产品的初步构思有一个可视化的展示。通过圆形展示，可以更加直观地了解项目的需求和提供的功能。

> 产品原型主要用于展示项目的功能，并不是最终的页面效果。




### 2.3 技术选型

<img src="https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/ruiji_project_imgs/技术选型.png?raw=true" alt="技术选型" style="zoom:50%;" />

### 2.4 功能架构

<img src="https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/ruiji_project_imgs/功能架构.png?raw=true" alt="功能架构" style="zoom:50%;" />

### 2.5 角色

* 后台系统管理员：登录后台管理系统，拥有后台系统中的所有操作权限
* 后台系统普通员工：登录后台管理系统，对菜品、套餐、订单等进行管理
* C端用户：登录移动端应用，可以浏览菜品、添加购物车、设置地址、在线下单

## 3. 开发环境搭建

### 3.1 数据库环境搭建

```sql
# 创建新数据库
create database ruiji;
# 导入数据模型
use ruiji;
source /Users/db_reggie.sql -- use terminal
```

| Tables_in_ruiji | 说明 |
| --- | ---|
| address_book    | 地址簿表 |
| category        | 菜品和套餐分类表 |
| dish            | 菜品单 |
| dish_flavor     | 菜品口味关系表 |
| employee        | 员工表 |
| order_detail    | 订单明细表 |
| orders          | 订单表 |
| setmeal         | 套餐表 |
| setmeal_dish    | 套餐菜品关系表 |
| shopping_cart   | 购物车表 |
| user            | 用户表（C端）|


### 3.2 maven项目搭建
1. 创建 maven 项目
2. 导入 `pom.xml` 文件


```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>3.1.1</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>com.itheima</groupId>
    <artifactId>ruiji_take_out</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>ruiji_take_out</name>
    <description>ruiji_take_out</description>
    <properties>
        <java.version>17</java.version>
    </properties>
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
            <version>3.0.2</version>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>com.mysql</groupId>
            <artifactId>mysql-connector-j</artifactId>
            <scope>runtime</scope>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter-test</artifactId>
            <version>3.0.2</version>
            <scope>test</scope>
        </dependency>

        <!-- https://mvnrepository.com/artifact/com.baomidou/mybatis-plus-boot-starter -->
        <dependency>
            <groupId>com.baomidou</groupId>
            <artifactId>mybatis-plus-boot-starter</artifactId>
            <version>3.5.3.1</version>
        </dependency>
        <!-- https://mvnrepository.com/artifact/com.alibaba.fastjson2/fastjson2 -->
        <dependency>
            <groupId>com.alibaba.fastjson2</groupId>
            <artifactId>fastjson2</artifactId>
            <version>2.0.34</version>
        </dependency>
        <!-- https://mvnrepository.com/artifact/com.alibaba/druid-spring-boot-starter -->
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid-spring-boot-starter</artifactId>
            <version>1.2.18</version>
        </dependency>
        <!-- https://mvnrepository.com/artifact/commons-lang/commons-lang -->
        <dependency>
            <groupId>commons-lang</groupId>
            <artifactId>commons-lang</artifactId>
            <version>2.6</version>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <excludes>
                        <exclude>
                            <groupId>org.projectlombok</groupId>
                            <artifactId>lombok</artifactId>
                        </exclude>
                    </excludes>
                </configuration>
            </plugin>
        </plugins>
    </build>

</project>
```

3. 配置 application.yml

```yml
server:
  port: 8080

spring:
  application:
    # 应用对应的名称，可选，默认为project名
    name: ruiji_take_out
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:8080/ruiji?useSSL=false;
    username: root
    password: Aa65937824!
    type: com.alibaba.druid.pool.DruidDataSource

mybatis-plus:
  configuration:
    # 在银蛇实体或者属性时，将数据库中表名和字段名中的下划线去掉，按照驼峰命名法映射
    # 表名: address_book --> AddressBook
    # 字段名: user_name -> userName
    map-underscore-to-camel-case: true
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
  global-config:
    db-config:
      id-type: assign_id

```
4. 编写启动类 (`RuijiTakeOutApplication.java`)

```java
package com.itheima;

import lombok.extern.slf4j.Slf4j;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@Slf4j
@SpringBootApplication
public class RuijiTakeOutApplication {

    public static void main(String[] args) {
        SpringApplication.run(RuijiTakeOutApplication.class, args);
        log.info("项目启动成功");
    }

}

```
 
## 4. 后台登录功能开发
### 4.1 需求分析
1. 页面原型展示

<img src="https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/ruiji_project_imgs/页面原型展示.png?raw=true" alt="页面原型展示" style="zoom:50%;" />

2. 登录页面展示
   
<img src="https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/ruiji_project_imgs/登录页面展示.png?raw=true" alt="登录页面展示" style="zoom:50%;" />

3. 查看登录请求信息

<img src="https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/ruiji_project_imgs/查看登录请求信息.png?raw=true" alt="查看登录请求信息" style="zoom:50%;" />



### 4.2 代码开发

1. 创建实体类 Employee，和 employee 表进行映射

   <img src="https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/ruiji_project_imgs/代码开发.png?raw=true" alt="代码开发" style="zoom:40%;" />

2. 创建Controller、Service、Dao

<img src="https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/ruiji_project_imgs/代码开发3.png?raw=true" alt="代码开发3" style="zoom:40%;" />

3. 导入返回结果类

此类是一个通用结果类，服务端响应的所有结果最终都会包装成此种类型返回给前端页面

<!-- <img src="https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/ruiji_project_imgs/结果类R.png?raw=true" alt="结果类R" style="zoom:50%;" /> -->

```java
package com.itheima.common;

import lombok.Data;

import java.util.HashMap;
import java.util.Map;

/**
 * 通用返回结果，服务端响应的数据最终都会封装成此对象
 * @param <T>
 */
@Data
public class R<T> {
    private Integer code; // 编码：1 成功， 0和其他数字 失败
    private String msg; // 错误信息
    private T data; // 数据：Employee对象或其他对象
    private Map map = new HashMap(); // 动态数据

    public static <T> R<T> sucess(T object) { // <T> 表示该方法是泛型方法
        R<T> r = new R<T>();
        r.data = object;
        r.code = 1;
        return r;
    }

    public static <T> R<T> error(String msg) {
        R<T> r = new R<T>();
        r.msg = msg;
        r.code = 0;
        return r;
    }

    public R<T> add (String key, Object value) {
        this.map.put(key, value);
        return this;
    }
}
```

4. 在Controller中创建登录方法


处理逻辑如下：
- 将页面提交的密码password进行md5加密处理
- 根据页面提交的用户名username查询数据库
- 如果没有查询到则返回登录失败结果
- 密码比对，如果不一致则返回登录失败结果
- 查看员工状态，如果为已禁用状态，则返回员工已禁用结果
- 登陆成功，将员工id存入Session并返回登陆成功结果

<img src="https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/ruiji_project_imgs/代码开发4.png?raw=true" alt="代码开发4" style="zoom:40%;" />

```java
    /**
     * 员工登录
     * @param request - 获取session， 将员工对象存储到session中，若需要获取当前用户可从session直接获取
     * @param employee
     * @return
     */
    @PostMapping("/login")
    public R<Employee> login(HttpServletRequest request, @RequestBody Employee employee) {
        // - 将页面提交的密码password进行md5加密处理
        String password = employee.getPassword();
        password = DigestUtils.md5DigestAsHex(password.getBytes());

        // - 根据页面提交的用户名username查询数据库
        LambdaQueryWrapper<Employee> queryWrapper = new LambdaQueryWrapper<>();
        queryWrapper.eq(Employee::getUsername, employee.getUsername());
        Employee emp = employeeService.getOne(queryWrapper);

        // - 如果没有查询到则返回登录失败结果
        if (emp == null) {
            return R.error("登录失败，该用户不存在");
        }

        // - 密码比对，如果不一致则返回登录失败结果
        if (!emp.getPassword().equals(password)) {
            return R.error("登录失败，密码错误");
        }

        // - 查看员工状态，如果为已禁用状态，则返回员工已禁用结果
        if (emp.getStatus() == 0) {
            return R.error("登录失败，账号已禁用");
        }

        // - 登陆成功，将员工id存入Session并返回登陆成功结果
        request.getSession().setAttribute("employee", emp);

        return R.sucess(emp);
    }
```

## 5. 后台退出功能开发
### 5.1 需求分析
员工登录成功后，页面跳转到后台系统首页面（backend/index.html），此时右上角会显示当前登录用户的姓名（管理员，开关），如果员工需要退出系统，直接点击右侧的退出按钮即可退出系统，退出系统后页面应跳转回登录页面
### 5.2 代码开发
用户点击页面中退出按钮，发送请求，请求地址为`/employee/logout`，请求方式为`POST`。
我们只需要在Controller中创建对应的处理方法即可，具体处理逻辑：

1. 清理Session中的用户id
2. 返回结果

```java
    /**
     * 员工登出
     * @param request
     * @return
     */
    @PostMapping("/logout")
    public R<String> login(HttpServletRequest request) {
        // 清理Session中的用户id
        request.getSession().removeAttribute("employee");
        return R.sucess("退出成功");
    }
```

### 5.3 功能测试
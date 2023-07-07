---
layout:     post
title:      "项目实战--瑞吉外卖Day06--手机验证码登录服务"
subtitle:   
date:       2023-07-02 16:00
author:     "Chenghao"
header-img: "img/post-bg-2015.jpg"
tags:
    - Spring
    - Springboot
    - project
---

> 笔记摘自：黑马程序员 | B站视频链接：<https://www.bilibili.com/video/BV13a411q753/>



# 手机验证码登录

## 1. 短信发送

### 1.1 短信服务介绍

目前市面上有很多第三方提供的短信服务，这些第三方短信服务会和各个运营商（移动、联通、电信）对接，我们需要注册成为会员并且按照提供的开发文档进行调用就可以发送短信。需要说明的是，这些短信服务一般都是收费服务。

常用短信服务：

* 阿里云（Short Message Service， sms）
  * 验证码
  * 短信通知
  * 推广短信
* 华为云
* 腾讯云
* 京东

### 1.2 阿里云短信服务

阿里云官网：<https://www.aliyun.com>

登录后进入短信服务管理页面，选择国内消息菜单：

<img src = "https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/ruiji_project_imgs/短信服务02.png?raw=true" alt = "短信服务02" style = "zoom: 30%"/>

短信签名是短信发送者的书名，表示发送方的身份

<img src = "https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/ruiji_project_imgs/短信服务03.png?raw=true" alt = "短信服务03" style = "zoom: 30%"/>

### 1.3 代码开发

具体步骤：

1. 导入maven坐标

```xml
<dependency>
  <groupId>com.aliyun</groupId>
  <artifactId>aliyun-java-sdk-core</artifactId>
  <version>4.5.16</version>
</dependency>
```



2. 调用API

## 2. 手机验证码登录

### 2.1 需求分析

为了方便用户登录，移动端通常会提供通过手机验证码登录的功能

优点：

* 方便快捷，无需注册，直接登录
* 使用短信验证码作为登录凭证，无需记忆密码
* 安全

登录流程：

输入手机号 > 获取验证码 > 输入验证码 > 点击登录 > 登陆成功

> 通过手机验证码登录，手机号是区分不同用户的标识



### 2.2 数据模型

<img src = "https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/ruiji_project_imgs/短信服务04.png?raw=true" alt = "短信服务04" style = "zoom: 50%"/>



### 2.3 代码开发

交互过程：

1. 在登录页面输入手机号，点击 `获取验证码` 按钮，页面发送ajax请求，在服务端调用短信服务API给指定手机号码发送验证码短信
2. 在登录页面输入验证码，点击 `登录` 按钮，发送ajax请求，在服务端处理登录请求

* 实体类User
* Dao接口UserDao
* 业务层接口UserService
* 业务层实现类UserServiceImpl
* 控制层UserController
* 工具类 SMSUtils、ValidateCodeUtils

修改 `LoginCheckFilter`

在进行手机验证码登录时，发送的请求需要再次过滤器处理时直接放行

```java
String[] urls = new String[]{
        "/employee/login",
        "/employee/logout",
        "/backend/**",
        "/front/**",
        "/common/**",
        "/user/sendMsg",
        "/user/login"
};

// 4-2. 判断登录状态，如果已登录，则直接放行
if (request.getSession().getAttribute("user") != null) {
    log.info("用户已登录，用户 id 为{}", request.getSession().getAttribute("user"));

    Long userId = (Long) request.getSession().getAttribute("user");
    BaseContext.setCurretnId(userId);
    filterChain.doFilter(request, response);
    return;
}
log.info("用户未登录");
```


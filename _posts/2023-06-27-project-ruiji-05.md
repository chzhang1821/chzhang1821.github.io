---
layout:     post
title:      "项目实战--瑞吉外卖Day05"
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



# 套餐管理业务开发

## 1. 效果展示

<img src = "https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/ruiji_project_imgs/套餐管理01.png?raw=true" alt = "套餐管理01" style = "zoom: 50%"/>

<img src = "https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/ruiji_project_imgs/套餐管理02.png?raw=true" alt = "套餐管理02" style = "zoom: 20%"/>



## 2. 新增套餐

### 2.1 需求分析

套餐就是菜品的集合

后台系统中可以管理套餐信息，通过新增套餐功能来添加一个新的套餐，在添加套餐时需要选择当前套餐所属的套餐分类和包含的菜品，并且需要上传套餐对应的图片，在移动端会按照套餐分类来展示对应的套餐



### 2.2 数据模型

新增套餐，其实就是将新增页面录入的套餐信息插入到setmeal表，还需要向setmeal_dish表插入套餐和菜品关联数据。所以在新增套餐时，涉及到两个表：

* setmeal 套餐表
* setmeal_dish 套餐菜品关系表



### 2.3 代码开发

* 实体类SetmealDish
* DTO SetmealDto
* Dao接口SetmealDishDao
* 业务层接口SetmealDishService
* 业务层实现类SetmealDIshServiceImpl
* 控制层SetmealController



交互过程：

1. 页面（backend/page/food/add.html）发送ajax请求，请求服务端获取套餐分类数据并展示到下拉框中
2. 页面发送ajax请求，请求服务端获取菜品分类数据并展示到添加菜品窗口中
3. 页面发送ajax请求，请求服务端，根据菜品分类查询对应的菜品数据并展示到添加菜品窗口中
4. 页面发送请求进行图片上传，请求服务端将图片保存到服务器
5. 页面发送请求进行图片下载，将上传的图片进行回显
6. 点击保存按钮，发送ajax请求，将套餐相关数据以json形式提交到服务端



### 2.4 功能测试




## 3. 套餐信息分页查询



## 4. 删除套餐






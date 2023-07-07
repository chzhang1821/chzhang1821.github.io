---
layout:     post
title:      "项目实战--瑞吉外卖Day07--菜单展示、购物车、下单"
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



# 菜单展示、购物车、下单

## 1. 导入用户地址簿相关功能代码

### 1.1 需求分析

地址簿，指的是移动端消费者用户的地址信息，用户登录成功后可以维护自己的地址信息。同一个用户可以有多个地址信息，但是只能有一个默认地址。

<img src = "https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/ruiji_project_imgs/地址簿01.png?raw=true" alt = "地址簿01" style = "zoom: 50%"/>

### 1.2 数据模型

address_book

<img src = "https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/ruiji_project_imgs/地址簿02.png?raw=true" alt = "地址簿02" style = "zoom: 50%"/>



## 2. 菜品展示

### 2.1 需求分析

用户登录成功后跳转到系统首页，在首页需要根据分类来展示菜品和套餐。如果菜品设置了口味信息，需要展示`选择规格`按钮，否则显示`+`按钮

<img src = "https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/ruiji_project_imgs/菜品展示.png?raw=true" alt = "菜品展示" style = "zoom: 50%"/>

### 2.2 代码开发

```java
/**
 * 根据条件查询菜品数据
 * @param dish
 * @return
 */
@GetMapping("/list")
public R<List<DishDto>> list(Dish dish) {
    LambdaQueryWrapper<Dish> queryWrapper = new LambdaQueryWrapper<>();
    queryWrapper.eq(dish.getCategoryId() != null, Dish::getCategoryId, dish.getCategoryId());
    queryWrapper.eq(Dish::getStatus, 1);
    queryWrapper.orderByAsc(Dish::getSort).orderByDesc(Dish::getUpdateTime);
    List<Dish> list = dishService.list(queryWrapper);


    List<DishDto> dishDtoList = list.stream().map(item -> {
        DishDto dishDto = new DishDto();

        BeanUtils.copyProperties(item, dishDto);
        Long categoryId = item.getCategoryId();

        Category category = categoryService.getById(categoryId);

        if (category != null) {
            String categoryName = category.getName();
            dishDto.setCategoryName(categoryName);
        }

        Long dishId = item.getId();
        LambdaQueryWrapper<DishFlavor> lambdaQueryWrapper = new LambdaQueryWrapper<DishFlavor>();
        lambdaQueryWrapper.eq(DishFlavor::getDishId, dishId);
        List<DishFlavor> flavors = dishFlavorService.list(lambdaQueryWrapper);
        dishDto.setFlavors(flavors);
        return dishDto;
    }).collect(Collectors.toList());
    return R.success(dishDtoList);
}


/**
 * 根据条件查询
 * @param setmeal
 * @return
 */
@GetMapping("/list")
public R<List<Setmeal>> list(Setmeal setmeal) {
    LambdaQueryWrapper<Setmeal> queryWrapper = new LambdaQueryWrapper<>();
    queryWrapper.eq(setmeal.getCategoryId() != null, Setmeal::getCategoryId, setmeal.getCategoryId());
    queryWrapper.eq(setmeal.getStatus() != null, Setmeal::getStatus, setmeal.getStatus());
    queryWrapper.orderByDesc(Setmeal::getUpdateTime);
    List<Setmeal> list = setmealService.list(queryWrapper);
    return R.success(list);
}
```

## 3. 购物车

### 3.1 需求分析

移动端用户可以将菜品或者套餐添加到购物车。对于菜品，如果设置了口味信息，则需要选择规格后才能加入购物车，对于套餐，可以直接点击`+`将当前套餐加入购物车，在购物车中可以修改菜品和套餐的数量，也可以清空购物车

<img src = "https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/ruiji_project_imgs/购物车01.png?raw=true" alt = "购物车01" style = "zoom: 50%"/>

### 3.2 数据模型

shopping_cart

<img src = "https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/ruiji_project_imgs/购物车02.png?raw=true" alt = "购物车02" style = "zoom: 50%"/>

### 3.3 代码开发

```java
package com.itheima.controller;

import com.baomidou.mybatisplus.core.conditions.query.LambdaQueryWrapper;
import com.itheima.common.BaseContext;
import com.itheima.common.R;
import com.itheima.entity.ShoppingCart;
import com.itheima.service.ShoppingCartService;
import lombok.extern.slf4j.Slf4j;
import org.apache.ibatis.annotations.Delete;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.cglib.core.Local;
import org.springframework.web.bind.annotation.*;

import java.awt.*;
import java.time.LocalDateTime;
import java.util.List;

@RestController
@RequestMapping("/shoppingCart")
@Slf4j
/**
 * 购物车
 */
public class ShoppingCartController {
    @Autowired
    private ShoppingCartService shoppingCartService;

    /**
     * 添加购物车
     * @param shoppingCart
     * @return
     */
    @PostMapping("/add")
    public R<ShoppingCart> add(@RequestBody ShoppingCart shoppingCart) {
        log.info("shopping car: " + shoppingCart);
        // 设置用户id
        Long currentId = BaseContext.getCurrentId();
        shoppingCart.setUserId(currentId);
        // 设置菜品数量，同一份加多次，则只需要加数量即可
        // 查询当前菜品或套餐是否在购物车中
        Long dishId = shoppingCart.getDishId();
        LambdaQueryWrapper<ShoppingCart> queryWrapper = new LambdaQueryWrapper<>();
        queryWrapper.eq(ShoppingCart::getUserId, currentId);
        if (dishId != null) {
            // 添加菜品
            queryWrapper.eq(ShoppingCart::getDishId, dishId);
        } else {
            // 添加套餐
            queryWrapper.eq(ShoppingCart::getSetmealId, shoppingCart.getSetmealId());
        }
        // 已经存在则在原来的数量上加1
        ShoppingCart cartServiceOne = shoppingCartService.getOne(queryWrapper);
        if (cartServiceOne != null) {
            Integer number = cartServiceOne.getNumber();
            cartServiceOne.setNumber(number + 1);
            shoppingCartService.updateById(cartServiceOne);
        } else {
            // 如果不存在，数量默认为1
            shoppingCart.setNumber(1);
            shoppingCartService.save(shoppingCart);
            shoppingCart.setCreateTime(LocalDateTime.now());
            cartServiceOne = shoppingCart;
        }

        return R.success(cartServiceOne);
    }

    /**
     * 查看购物车
     * @return
     */
    @GetMapping("/list")
    public R<List<ShoppingCart>> list() {
        log.info("查看购物车...");
        LambdaQueryWrapper<ShoppingCart> queryWrapper = new LambdaQueryWrapper<>();
        queryWrapper.eq(ShoppingCart::getUserId, BaseContext.getCurrentId());
        queryWrapper.orderByAsc(ShoppingCart::getCreateTime);
        List<ShoppingCart> list = shoppingCartService.list(queryWrapper);
        return R.success(list);
    }

    /**
     * 清空购物车
     * @return
     */
    @DeleteMapping("/clean")
    public R<String> clean() {
        LambdaQueryWrapper<ShoppingCart> queryWrapper = new LambdaQueryWrapper<>();
        queryWrapper.eq(ShoppingCart::getUserId, BaseContext.getCurrentId());
        shoppingCartService.remove(queryWrapper);
        return R.success("清空购物车成功");
    }
}
```



### 3.4 功能测试


## 4. 用户下单

### 4.1 需求分析

移动端用户将菜品或套餐加入购物车后，可以店家购物车中的`去结算`按钮，页面跳转到订单确认页面，点击`去支付`按钮则完成下单操作

<img src = "https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/ruiji_project_imgs/用户下单01.png?raw=true" alt = "用户下单01" style = "zoom: 50%"/>

### 4.2 数据模型

orders

<img src = "https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/ruiji_project_imgs/支付01.png?raw=true" alt = "支付01" style = "zoom: 50%"/>

order_detail

<img src = "https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/ruiji_project_imgs/支付02.png?raw=true" alt = "支付02" style = "zoom: 50%"/>

### 4.3 代码开发

  ```java
  package com.itheima.service.impl;
  
  import com.baomidou.mybatisplus.core.conditions.query.LambdaQueryWrapper;
  import com.baomidou.mybatisplus.core.toolkit.IdWorker;
  import com.baomidou.mybatisplus.extension.service.impl.ServiceImpl;
  import com.itheima.common.BaseContext;
  import com.itheima.common.CustomException;
  import com.itheima.dao.OrderDao;
  import com.itheima.dao.OrderDetailDao;
  import com.itheima.entity.*;
  import com.itheima.service.*;
  import org.springframework.beans.factory.annotation.Autowired;
  import org.springframework.stereotype.Service;
  import org.springframework.transaction.annotation.Transactional;
  
  import java.math.BigDecimal;
  import java.time.LocalDateTime;
  import java.util.List;
  import java.util.concurrent.atomic.AtomicInteger;
  import java.util.stream.Collectors;
  
  @Service
  public class OrderServiceImpl extends ServiceImpl<OrderDao, Orders> implements OrderService {
      @Autowired
      private ShoppingCartService shoppingCartService;
      @Autowired
      private UserService userService;
      @Autowired
      private AddressBookService addressBookService;
      @Autowired
      private OrderDetailService orderDetailService;
  
      /**
       * 用户下单
       *
       * @param orders
       */
      @Override
      @Transactional
      public void submit(Orders orders) {
          // 当前用户是谁
          Long currentId = BaseContext.getCurrentId();
          // 查询当前用户的购物车数据
          LambdaQueryWrapper<ShoppingCart> queryWrapper = new LambdaQueryWrapper<>();
          queryWrapper.eq(ShoppingCart::getUserId, currentId);
          List<ShoppingCart> shoppingCarts = shoppingCartService.list(queryWrapper);
  
          if (shoppingCarts == null || shoppingCarts.size() == 0) {
              throw new CustomException("购物车为空，不能下单");
          }
  
          // 查询用户信息
          User user = userService.getById(currentId);
          // 查询地址数据
          Long addressBookId = orders.getAddressBookId();
          AddressBook addressBook = addressBookService.getById(addressBookId);
  
          if (addressBookId == null) {
              throw new CustomException("用户地址信息有误，不能下单");
          }
          // 完成下单，向订单表插入一条数据
          long orderId = IdWorker.getId();
  
          AtomicInteger amount = new AtomicInteger(0); // 原子操作，保证多线程时安全
  
          List<OrderDetail> orderDetails = shoppingCarts.stream().map(item -> {
              OrderDetail orderDetail = new OrderDetail();
              orderDetail.setOrderId(orderId);
              orderDetail.setNumber(item.getNumber());
              orderDetail.setDishFlavor(item.getDishFlavor());
              orderDetail.setSetmealId(item.getSetmealId());
              orderDetail.setName(item.getName());
              orderDetail.setImage(item.getImage());
              orderDetail.setAmount(item.getAmount());
              amount.addAndGet(item.getAmount().multiply(new BigDecimal(item.getNumber())).intValue());
              return orderDetail;
          }).collect(Collectors.toList());
  
          orders.setNumber(String.valueOf(orderId));
          orders.setId(orderId);
          orders.setOrderTime(LocalDateTime.now());
          orders.setCheckoutTime(LocalDateTime.now());
          orders.setStatus(2);
          orders.setAmount(new BigDecimal(amount.get()));
          orders.setUserId(currentId);
  //        orders.setNumber();
          orders.setUserName(user.getName());
          orders.setConsignee(addressBook.getConsignee());
          orders.setPhone(addressBook.getPhone());
          orders.setAddress((addressBook.getProvinceName() == null ? "" : addressBook.getProvinceName())
                  + (addressBook.getCityName() == null ? "" : addressBook.getCityName())
                  + (addressBook.getDistrictName() == null ? "" : addressBook.getDistrictName())
                  + (addressBook.getDetail() == null ? "" : addressBook.getDetail()));
          this.save(orders);
          // 订单明细表插入多条数据
          orderDetailService.saveBatch(orderDetails);
          // 清空购物车数据
          shoppingCartService.remove(queryWrapper);
      }
  }
  ```




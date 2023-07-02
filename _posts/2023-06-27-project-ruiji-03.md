---
layout:     post
title:      "项目实战--瑞吉外卖Day03"
subtitle:   
date:       2023-06-27 16:00
author:     "Chenghao"
header-img: "img/post-bg-2015.jpg"
tags:
    - Spring
    - Springboot
    - project
---

# 分类管理业务开发



## 1. 效果展示

<img src = "https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/ruiji_project_imgs/分类管理效果展示.png?raw=true" alt = "分类管理效果展示" style = "zoom: 50%"/>



## 2. 公共字段自动填充

### 2.1 问题分析

前面我们已经完成了后台系统的员工管理功能开发，在新增员工时需要设置创建时间、创建人、修改时间、修改人等字段，在编辑员工时需要设置修改时间和修改人字段。这些字段属于公共字段，也就是很多表中都有这些字段，如下：

能不能对于这些公共字段在某个地方统一处理，来简化开发？

答案就是使用 Mybatis Plus 提供的==公共字段填充==功能

### 2.2 代码实现

Mybatis Plus 公共字段自动填充，也就是在插入或者更新的时候为指定字段赋予指定的值，使用它的好处就是可以统一对这些字段进行处理，避免了重复代码。

实现步骤：

1. 在实体类的属性上加入 `@TableField` 注解，指定自动填充的策略

```java
@Data
public class Employee implements Serializable {
    private static final long serialVersionUID = 1L;
    private Long id;
    private String username;
    private String name;
    private String password;
    private String phone;
    private String sex;
    private String idNumber; // 身份证号码
    private Integer status;

    @TableField(fill = FieldFill.INSERT) // 插入时填充字段
    private LocalDateTime createTime;

    @TableField(fill = FieldFill.INSERT_UPDATE) // 插入和更新时都填充字段
    private LocalDateTime updateTime;

    @TableField(fill = FieldFill.INSERT)
    private Long createUser;

    @TableField(fill = FieldFill.INSERT_UPDATE)
    private Long updateUser;
}
```



2. 按照框架要求编写元数据对象处理器，在此类中统一为公共字段赋值，此类需要实现 `MetaObjectHandler` 接口

```java
/**
 * 元数据对象处理器
 */
@Component
@Slf4j
public class MyMetaObjectHandler implements MetaObjectHandler {
    /**
     * 插入时自动填充
     * @param metaObject
     */
    @Override
    public void insertFill(MetaObject metaObject) {
        log.info("公共字段自动填充[insert]...");
        log.info(metaObject.toString());
        metaObject.setValue("createTime", LocalDateTime.now());
        metaObject.setValue("updateTime", LocalDateTime.now());
        metaObject.setValue("createUser", (long) 1);
        metaObject.setValue("updateUser", (long) 1);
    }

    /**
     * 更新时自动填充
     * @param metaObject
     */
    @Override
    public void updateFill(MetaObject metaObject) {
        log.info("公共字段自动填充[update]...");
        log.info(metaObject.toString());
//        metaObject.setValue("createTime", LocalDateTime.now());
        metaObject.setValue("updateTime", LocalDateTime.now());
//        metaObject.setValue("createUser", (long) 1);
        metaObject.setValue("updateUser", (long) 1);
    }
}
```

> 注意：当前我们设置的 `createUser` 和 `updateUser`为固定值，后面我们需要进行改造，改为动态获得当前登录用户的id

### 2.3 代码测试



### 2.4 功能完善

在学习ThreadLocal之前，我们需要先确认一个事情，就是客户端发送的每次http请求，对应的在服务端都会分配一个新的线程来处理，在处理过程中涉及到下面类中的方法都属于相同的一个线程：

1. LoginCheckFilter的doFilter方法
2. EmployeeController的update方法
3. MyMetaObjectHandler的updateFill方法

可以在上面的三个方法中分别加入下面的代码（获取当前线程id）

```java
long id = Thread.currentThread().getId();
log.info("线程id：{}", id);
```

执行编辑员工功能进行验证，通过观察控制台输出可以发现，一次请求对应的线程id是相同的

```java
2023-07-02T10:20:55.953+08:00  INFO 91113 --- [nio-8080-exec-2] com.itheima.filter.LoginCheckFilter      : 线程id：39
  
2023-07-02T10:20:55.992+08:00  INFO 91113 --- [nio-8080-exec-2] c.itheima.controller.EmployeeController  : 线程id：39

2023-07-02T10:20:55.996+08:00  INFO 91113 --- [nio-8080-exec-2] com.itheima.common.MyMetaObjectHandler   : 线程id：39
```



什么是ThreadLocal？

ThreadLocal 并不是一个 Thread，而是 Thread 的局部变量。当使用ThreadLocal 维护变量时，ThreadLocal为每个使用该变量的线程提供独立的变量副本，所以每一个线程都可以独立地改变自己的副本，而不会影响其他线程所对应的副本。

ThreadLocal 为每个线程提供单独一份存储空间，具有线程隔离的效果，只有在线程内才能获取到对应的值，线程外则不能访问。

ThreadLocal常用方法：

* `public void set(T value)` 设置当前线程的线程局部变量的值
* `public T get()` 返回当前线程所对应的线程局部变量的值

我们可以在 LoginCheckFilter 的 doFilter 方法中获取当前登录用户 id， 并调用 ThreadLocal 的set方法来设置当前线程的线程局部变量的值（用户id），然后在MyMetaObjectHandler的updateFill方法中调用ThreadLocal的get方法来获得当前线程所对应的线程局部变量的值（用户id）



实现步骤：

1. 编写BaseContext工具类，基于ThreadLocal封装的工具类
2. 在LoginCheckFilter的doFilter方法中调用BaseContext来设置当前登录用户的id
3. 在MyMetaObjectHandler的方法中调用BaseContext获取当前用户的id

```java
/**
 * 基于ThreadLocal封装工具类，用户保存和获取当前登录用户id
 */
public class BaseContext {
    private static ThreadLocal<Long> threadLocal = new ThreadLocal<>();

    public static void setCurretnId(Long id) {
        threadLocal.set(id);
    }

    public static Long getCurrentId() {
        return threadLocal.get();
    }
}
```

> 笔记摘自：黑马程序员 | B站视频链接：<https://www.bilibili.com/video/BV13a411q753/>

## 3. 新增分类

### 3.1 需求分析

后台系统中可以管理分类信息，分类包括两种类型，分别是菜品分类和套餐分类。当我们在后台系统中天假菜品时需要选择一个菜品分类，当我们在后台系统中添加一个套餐时需要选择一个套餐分类，在移动端也会按照菜品分类和套餐分类来展示对应的菜品和套餐。

<img src = "https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/ruiji_project_imgs/新增分类需求分析.png?raw=true" alt = "新增分类需求分析" style = "zoom: 50%"/>

可以在后台系统的分类管理页面分别添加菜品分类和套餐分类。

<img src = "https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/ruiji_project_imgs/菜品和套餐分类.png?raw=true" alt = "菜品和套餐分类" style = "zoom: 50%"/>

### 3.2 数据模型

新增分类，其实就是将我们新增窗口录入的分类数据插入到category表，表结构如下：

<img src = "https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/ruiji_project_imgs/category表.png?raw=true" alt = "category表" style = "zoom: 40%"/>

### 3.3 代码开发

在开发业务功能前，现将需要用到的类和接口基本结构创建好：

* 实体类Category
* Dao接口CategoryDao
* 业务层接口CategoryService
* 业务层实现类CategoryServiceImpl
* 控制层CategoryController

程序执行过程：

1. 页面（backend/page/category/list.html）发送ajax请求，将新增分类窗口输入的数据以json形式提交到服务端
2. 服务端Controller接收页面提交的数据并调用Service将数据进行保存
3. Service调用Mapper操作数据库，保存数据



### 3.4 功能测试



## 4. 分类信息分页查询

### 4.1 需求分析

系统中的分类很多的时候，如果在一个页面中去哪不展示出来会显得比较乱，不便于查看，所以一般的系统中都会以分页的方式来展示列表数据。

<img src = "https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/ruiji_project_imgs/分类信息分页查询.png?raw=true" alt = "分类信息分页查询" style = "zoom: 40%"/>



### 4.2 代码开发

程序执行过程：

1. 页面发送ajax请求，将分页查询参数(page, pageSize, name)提交到服务器
2. 服务端Controller接收页面提交的数据并调用Service查询数据
3. Service调用Mapper操作数据库，查询分页数据
4. Controller将查询到的分页数据先应给页面
5. 页面接受到分页数据并通过ElementUI的Table组件展示到页面上

### 4.3 功能测试



## 5. 删除分类

### 5.1 需求分析

在分类管理列表页面，可以对某个分类进行删除操作。需要注意的是当分类管理按了菜品或者套餐时，此分类不允许删除。

<img src = "https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/ruiji_project_imgs/删除分类.png?raw=true" alt = "删除分类" style = "zoom: 40%"/>



### 5.2 代码开发

程序执行过程：

1. 页面发送ajax请求，将参数(id)提交到服务器
2. 服务端Controller接收页面提交的数据并调用Service删除数据
3. Service调用Mapper操作数据库

```java
/**
 * 根据id删除分类
 * @param id
 * @return
 */
@DeleteMapping
public R<String> delete(Long id) {
    log.info("删除分类，id为{}", id);

    categoryService.removeById(id);
    return R.success("分类信息删除成功");
}
```

### 5.3 功能测试



### 5.4 功能完善

删除分类是需要检查分类是否关联了菜品或者套餐

准备工作：

1. 实体类Dish和Setmeal
2. Dao接口DishDao和SetmealDao
3. 业务层接口DishService和SetmealService
4. 业务层实现类DishServiceImpl和SetmealServiceImpl



## 6. 修改分类

### 6.1 需求分析

在分类管理列表页面点击修改按钮，弹出修改窗口，在修改窗口辉县分类信息并进行修改，最后点击确定按钮完成修改操作



### 6.2 代码开发

```java
/**
 * 根据id修改分类信息
 * @param category
 * @return
 */
@PutMapping
public R<String> update(@RequestBody Category category) {
    log.info("修改分类信息：{}", category);
    categoryService.updateById(category);
    return R.success("修改分类信息成功");
}
```


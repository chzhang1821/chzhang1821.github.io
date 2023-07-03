---
layout:     post
title:      "项目实战--瑞吉外卖Day04"
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



# 菜品管理业务开发

## 1. 效果展示

<img src = "https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/ruiji_project_imgs/菜品管理01.png?raw=true" alt = "菜品管理01" style = "zoom: 50%"/>

## 2. 文件上传下载

### 2.1 文件上传介绍

文件上传，也称为upload，是指将本地图片、视频、音频等文件上传到服务器，可以供其他用户浏览或下载的过程。文件上传在项目中应用非常广泛，我们经常发微博、发微信朋友圈都用到了文件上传功能。

文件上传时，对页面的form表单有如下要求：

* `method="post"`	采用post方式提交数据
* `enctype="multipart/form-data"` 采用multipart格式上传文件
* `type="file"` 使用input的file控件上传

```html
<form method="post" action="/common/upload" enctype="multipart/form-data">
  <input name="myFile" type="file"/>
  <input type="submit" value="提交"/>
</form>
```

目前一些前端组件库也提供了相应的上传组件，但是底层原理还是基于form表单的文件上传。例如ElementUI提供的upload组件



服务端要接受客户端页面上传的文件，通常会使用Apache的两个组件：

* `commons-fileupload`
* `commons-io`

Spring 框架在 spring-web 包中对文件上传进行了封装，简化了服务端代码，只需要在Controller的方法中声明一个MultipartFile类型的参数即可接受上传的文件，例如：

```java

```



### 2.2 文件下载介绍

文件下载，也称为download，是指将文件从服务器传输到本地计算机的过程

通过浏览器进行文件下载，通常有两种表现形式：

* 以附件形式下载，弹出保存对话框，将文件保存到指定磁盘目录
* 直接在浏览器中打开

通过浏览器进行文件下载，本质上就是服务端将文件以流的形式写回浏览器的过程 



### 2.3 文件上传代码实现

文件上传，页面端可以用ElementUI提供的上传组件

可以直接使用资料中提供的上传页面

```html
<el-upload class="avatar-uploader"
            action="/common/upload"
            :show-file-list="false"
            :on-success="handleAvatarSuccess"
            :before-upload="beforeUpload"
            ref="upload">
    <img v-if="imageUrl" :src="imageUrl" class="avatar"></img>
    <i v-else class="el-icon-plus avatar-uploader-icon"></i>
</el-upload>
```



### 2.4 文件上传代码实现

```java
@PostMapping("/upload")
public R<String> upload(MultipartFile file) {
    // file是一个临时文件，需要转存到指定位置，否则本次请求完成后临时文件会删除
    log.info(file.toString());

    // 原始文件名
    String originFilename = file.getOriginalFilename();
    String suffix = originFilename.substring(originFilename.lastIndexOf("."));

    // 使用UUID重新生成文件名，防止文件名重复造成文件名覆盖
    String fileName = UUID.randomUUID().toString() + suffix;

    // 创建目录对象
    File dir = new File(basePath);
    if (!dir.exists()) {
        // 目录不存在，需要创建
        dir.mkdirs();
    }

    try {
        file.transferTo(new File(basePath + fileName));
    } catch (IOException e) {
        throw new RuntimeException(e);
    }
    return R.success(fileName);
}
```



### 2.5 文件下载代码实现

```java
/**
 * 文件下载
 * @param name
 * @param response
 */
@GetMapping("/download")
public void download(String name, HttpServletResponse response) {
    try {
        // 输入流，通过输入流读取文件内容
        FileInputStream fileInputStream = new FileInputStream(new File(basePath + name));
        // 输出流，通过输出流将文件写回浏览器，在浏览器展示图片
        ServletOutputStream outputStream = response.getOutputStream();
        response.setContentType("image/.jepg");
        byte[] bytes = new byte[1024];
        int len = 0;
        while ((len = fileInputStream.read(bytes)) != -1) {
            outputStream.write(bytes, 0, len);
            outputStream.flush();
        }
        outputStream.close();
        fileInputStream.close();
    } catch (FileNotFoundException e) {
        throw new RuntimeException(e);
    } catch (IOException e) {
        throw new RuntimeException(e);
    }
}
```



## 3. 新增菜品

### 3.1 需求分析

后台系统中可以管理菜品信息，通过新增功能来添加一个新的菜品，再添加菜品时需要选择当前菜品所属的菜品分类，并且需要上传菜品图片，在移动端会按照菜品分类来展示对应的菜品信息

### 3.2 数据模型

新增菜品，其实就是将新增页面录入的菜品信息插入到dish表，如果添加了口味做法，还需要向dish_flavor表插入数据。

所以在新增菜品时，涉及到两个表：

* dish 菜品表
* dish_flavor 菜品口味表

### 3.3 代码开发

导入DTO：

用于封装页面提交的数据

```java
@Data
public class DishDto extends Dish {

    private List<DishFlavor> flavors = new ArrayList<>();

    private String categoryName;

    private Integer copies;
}
```

> DTO，全程为Data Transfer Object，即数据传输对象，一般用于展示层与服务层之间的数据传输



* 实体类DishFlavor
* Dao接口DishFlavorDao
* 业务层接口DishFlavorService
* 业务层实现类DishFlavorServiceImpl
* 控制层DishController

交互过程：

1. 页面（backend/page/food/add.html）发送ajax请求，请求服务端获取菜品分类数据并展示到下拉框中
2. 页面发送请求进行图片上传，请求服务端将图片保存到服务器
3. 页面发送请求进行图片下载，将上传的图片进行回显
4. 点击保存按钮，发送ajax请求，将菜品相关数据以json形式提交到服务端

```java
@Service
public class DishServiceImpl extends ServiceImpl<DishDao, Dish> implements DishService {
    @Autowired
    private DishFlavorService dishFlavorService;
    /**
     * 新增菜品，同时保存对应的口味数据
     * @param dishDto
     */
    @Override
    @Transactional
    public void saveWithFlavor(DishDto dishDto) {
        // 保存菜品基本信息到菜品表dish
        this.save(dishDto);
        Long dishId = dishDto.getId();

        List<DishFlavor> flavors = dishDto.getFlavors();
        flavors.stream().map(item -> {
            item.setDishId(dishId);
            return item;
        }).collect(Collectors.toList());

        dishFlavorService.saveBatch(flavors);
    }
}

@RestController
@RequestMapping("/dish")
@Slf4j
public class DishController {
    @Autowired
    private DishFlavorService dishFlavorService;
    @Autowired
    private DishService dishService;

    /**
     * 新增菜品
     * @param dishDto
     * @return
     */
    @PostMapping
    public R<String> save(@RequestBody DishDto dishDto) {
        log.info(dishDto.toString());
        dishService.saveWithFlavor(dishDto);
        return R.success("新增菜品成功");
    }
}
```



### 3.4 功能测试





## 4. 菜品信息分页查询

### 4.1 需求分析

系统中的分类很多的时候，如果在一个页面中去哪不展示出来会显得比较乱，不便于查看，所以一般的系统中都会以分页的方式来展示列表数据。

### 4.3 代码开发

程序执行过程：

1. 页面发送ajax请求，将分页查询参数(page, pageSize, name)提交到服务器
2. 页面发送请求，请求服务端进行图片下载，用于页面图片展示

```java
/**
 * 菜品信息分页
 * @param page
 * @param pageSize
 * @param name
 * @return
 */
@GetMapping("/page")
public R<Page> page(int page, int pageSize, String name) {
    Page<Dish> pageInfo = new Page<>(page, pageSize);
    Page<DishDto> dishDtoPage = new Page<>(page, pageSize);
    LambdaQueryWrapper<Dish> queryWrapper = new LambdaQueryWrapper<>();
    queryWrapper.like(name != null, Dish::getName, name);
    queryWrapper.orderByDesc(Dish::getUpdateTime);
    dishService.page(pageInfo, queryWrapper);

    BeanUtils.copyProperties(pageInfo, dishDtoPage, "records");

    List<Dish> records = pageInfo.getRecords();
    List<DishDto> list = records.stream().map(item -> {
        DishDto dishDto = new DishDto();
        BeanUtils.copyProperties(item, dishDto);
        Long categoryId = item.getCategoryId();

        Category category = categoryService.getById(categoryId);
        if (category != null) {
            String categoryName = category.getName();
            dishDto.setCategoryName(categoryName);
        }

        return dishDto;
    }).collect(Collectors.toList());

    dishDtoPage.setRecords(list);

    return R.success(dishDtoPage);
}
```



### 4.4 功能测试



## 5. 修改菜品

### 5.1 需求分析



### 5.2 代码开发

交互过程：

1. 页面发送ajax请求，请求服务端获取分类数据，用于菜品分类下拉框中数据展示
2. 页面发送ajax请求，请求服务端，根据ID查询当前菜品信息，用于菜品信息回显
3. 页面发送请求，请求服务端进行图片下载，用于页图片回显
4. 点击保存按钮，页面发送ajax请求，将修改后的菜品相关数据以json形式提交到服务端



```java
//dishServiceImpl.java
@Override
public DishDto getByIdWithFlavor(Long id) {
    Dish dish = this.getById(id);
    DishDto dishDto = new DishDto();
    BeanUtils.copyProperties(dish, dishDto);

    LambdaQueryWrapper<DishFlavor> queryWrapper = new LambdaQueryWrapper<>();
    queryWrapper.eq(DishFlavor::getDishId, id);
    List<DishFlavor> flavors = dishFlavorService.list(queryWrapper);
    dishDto.setFlavors(flavors);
    return dishDto;
}

@Override
@Transactional
public void updateWithFlavor(DishDto dishDto) {
    this.updateById(dishDto);

    LambdaQueryWrapper<DishFlavor> queryWrapper = new LambdaQueryWrapper<>();
    queryWrapper.eq(DishFlavor::getId, dishDto.getId());
    dishFlavorService.remove(queryWrapper);

    List<DishFlavor> flavors = dishDto.getFlavors();
    flavors.stream().map(item -> {
        item.setDishId(dishDto.getId());
        return item;
    }).collect(Collectors.toList());

    dishFlavorService.saveBatch(flavors);
}

// DishController
/**
 * 根据id查询菜品信息和对应口味信息
 * @param id
 * @return
 */
@GetMapping("/{id}")
public R<DishDto> get(@PathVariable Long id) {
    DishDto dishDto = dishService.getByIdWithFlavor(id);

    return R.success(dishDto);
}
```





### 5.4 功能测试


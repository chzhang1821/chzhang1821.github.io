---
layout:     post
title:      "项目实战--瑞吉外卖Day08--缓存优化"
subtitle:   
date:       2023-07-13 16:00
author:     "Chenghao"
header-img: "img/post-bg-2015.jpg"
tags:
    - Spring
    - Springboot
    - project
---

> 笔记摘自：黑马程序员 | B站视频链接：<https://www.bilibili.com/video/BV13a411q753/>



## 1. 问题说明

**用户数量多，系统访问量大，频繁访问数据库，系统性能下降，用户体验差**，使用缓存优化，内存运行效率远高于数据库查询操作

<img src = "https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/ruiji_project_imgs/缓存优化.png?raw=true" alt = "缓存优化" style = "zoom: 50%" />

## 2. 环境搭建

### 2.1 maven坐标

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

### 2.2 配置文件

在项目的`application.yml`中加入redis相关配置：

```yaml
spring:
	data:
    redis:
      host: 106.14.221.8
      port: 6379
      password: Aa65937824
      database: 0
```



### 2.3 配置类

可以不创建，springboot会自动创建

```java
@Configuration
public class RedisConfig extends CachingConfigurerSupport {
    @Bean
    public RedisTemplate<Object, Object> redisTemplate(RedisConnectionFactory connectionFactory) {
        RedisTemplate<Object, Object> redisTemplate = new RedisTemplate<>();
        redisTemplate.setKeySerializer(new StringRedisSerializer());
        redisTemplate.setConnectionFactory(connectionFactory);
        return redisTemplate;
    }
}
```



## 3. 缓存短信验证码

### 3.1 实现思路

前面我们已经实现了移动端手机验证码登录，随机生成的验证码我们是保存在HttpSession中的。

现在需要改造为将验证码缓存在Redis中，具体实现思路如下：

1. 在服务端UserController中注入RedisTemplate对象，用于操作Redis
2. 在服务端UserController的sendMsg方法中，将随机生成的验证码缓存到Redis中，并设置有效期为5分钟
3. 在服务端UserController的login方法中，从Redis中获取缓存的验证码，如果登陆成功则删除Redis中的验证码

### 3.2 代码改造

```java
@RestController
@RequestMapping("/user")
@Slf4j
public class UserController {
    @Autowired
    private UserService userService;
    @Autowired
    private RedisTemplate redisTemplate;

    /**
     * 发送手机短信验证码
     * @param user
     * @return
     */
    @PostMapping("/sendMsg")
    public R<String> sendMsg(@RequestBody User user, HttpSession session) {
        // 获取手机号
        String phone = user.getPhone();
        log.info("手机号: {}", phone);
        if (StringUtils.isNotEmpty(phone)) {
            // 生成随机的4位验证码
            String code = ValidateCodeUtils.generateValidateCode(4).toString();
            log.info("code = {}", code);
            // 调用阿里云API完成发送短信
//            SMSUtils.sendMessage("瑞吉外卖", "", phone, code);

//            // 需要将生成的验证码保存到Session
//            session.setAttribute(phone, code);

            // 将生成的验证码缓存到Redis中，并设置有效期5min
            redisTemplate.opsForValue().set(phone, code, 5, TimeUnit.MINUTES);
            R.success("短信发送成功");
        }
        return R.error("短信发送失败");
    }

    /**
     * 移动端用户登录
     * @param map, session
     * @return
     */
    @PostMapping("/login")
    public R<User> login(@RequestBody Map map, HttpSession session) {
        log.info(map.toString());

        // 获取手机号
        String phone = map.get("phone").toString();
        // 获取验证码
        String code = map.get("code").toString();
//        // 从Session中获取保存的验证码
//        String codeInSession = (String) session.getAttribute(phone);
        // 从Redis中获取缓存的验证码
        String codeInSession = (String) redisTemplate.opsForValue().get(phone);
        // 进行验证码比对
        if (codeInSession != null && codeInSession.equals(code)) {
            // 登录成成
            // 若为新用户，自动完成注册
            LambdaQueryWrapper<User> queryWrapper = new LambdaQueryWrapper<>();
            queryWrapper.eq(User::getPhone, phone);
            User user = userService.getOne(queryWrapper);
            if (user == null) {
                user = new User();
                user.setPhone(phone);
                user.setStatus(1);
                userService.save(user);
            }
            session.setAttribute("user", user.getId());
            // 如果用户登录成功，则删除缓存的验证码
            redisTemplate.delete(phone);
            return R.success(user);
        }
        return R.error("登录失败");
    }
}
```



## 4. 缓存菜品数据

### 4.1 实现思路

前面我们已经实现了移动端菜品查看功能，对应的服务端方法为DishController的list方法，此方法会根据前段提交的查询条件进行数据库查询操作。在高并发的情况下，频繁查询数据会导致系统性能下降，服务端响应时间增长。现在需要对此方法进行缓存优化，提高系统的性能

具体实现思路如下：

1. 改造DishController中的list方法，先从Redis中获取菜品数据，如果有则直接返回，无需查询数据库；如果没有则查询数据库，并将查询到的菜品数据放入到Redis中。
2. 改造DishController的save和update方法，加入清理缓存的逻辑

> 在使用缓存过程中，要注意保证数据库中的数据和缓存中的数据一致，如果数据库中的数据发生变化，需要及时清理缓存数据



### 4.2 代码改造

```java
@RestController
@RequestMapping("/dish")
@Slf4j
public class DishController {
    @Autowired
    private DishFlavorService dishFlavorService;
    @Autowired
    private DishService dishService;
    @Autowired
    private CategoryService categoryService;
    @Autowired
    private RedisTemplate redisTemplate;

    /**
     * 新增菜品
     * @param dishDto
     * @return
     */
    @PostMapping
    public R<String> save(@RequestBody DishDto dishDto) {
        log.info(dishDto.toString());
        dishService.saveWithFlavor(dishDto);
        String key = "dish_" + dishDto.getCategoryId() + "_1";
        redisTemplate.delete(key);
        return R.success("新增菜品成功");
    }

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

    /**
     * 修改菜品
     * @param dishDto
     * @return
     */
    @PutMapping
    public R<String> update(@RequestBody DishDto dishDto) {
        log.info(dishDto.toString());
        dishService.updateWithFlavor(dishDto);
        String key = "dish_" + dishDto.getCategoryId() + "_1";
        redisTemplate.delete(key);
        return R.success("新增菜品成功");
    }


    /**
     * 根据条件查询菜品数据
     * @param dish
     * @return
     */
    @GetMapping("/list")
    public R<List<DishDto>> list(Dish dish) {
        List<DishDto> dishDtoList = null;
        String key = "dish_" + dish.getCategoryId() + "_" + dish.getStatus();
        // 从Redis中获取缓存数据
        dishDtoList = (List<DishDto>) redisTemplate.opsForValue().get(key);

        if (dishDtoList != null) {
            // 如果存在，直接返回，无需查询数据库
            return R.success(dishDtoList);
        }

        LambdaQueryWrapper<Dish> queryWrapper = new LambdaQueryWrapper<>();
        queryWrapper.eq(dish.getCategoryId() != null, Dish::getCategoryId, dish.getCategoryId());
        queryWrapper.eq(Dish::getStatus, 1);
        queryWrapper.orderByAsc(Dish::getSort).orderByDesc(Dish::getUpdateTime);
        List<Dish> list = dishService.list(queryWrapper);


        dishDtoList = list.stream().map(item -> {
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

        // 如果不存在，需要查询数据库
        redisTemplate.opsForValue().set(key, dishDtoList, 60, TimeUnit.MINUTES);

        return R.success(dishDtoList);
    }
}
```



## 5. Spring Cache

### 5.1 Spring Cache 介绍

Spring Cache 是一个框架，实现了基于注解的缓存功能，只需要简单地加一个注解，就能实现缓存功能。

Spring Cache 提供了一层抽象，底层可以切换不同的cache实现。具体就是通过 CacheManager 接口来统一不同的缓存技术。

CacheManager是Spring提供的各种缓存技术抽象接口。

| CacheManager        | 描述                               |
| ------------------- | ---------------------------------- |
| EhCacheCacheManager | 使用EhCache作为缓存技术            |
| GuavaCacheManager   | 使用Google的GuavaCache作为缓存技术 |
| RedisCacheManager   | 使用Redis作为缓存技术              |

### 5.2 Spring Cache 常用注解

| 注解             | 说明                                                         |
| ---------------- | ------------------------------------------------------------ |
| `@EnableCaching` | 开启缓存注解功能                                             |
| `@Cacheable`     | 在方法执行前spring先查看缓存中是否有数据，如果有数据，则直接返回缓存数据；若没有数据，调用方法并将方法返回值放到缓存中 |
| `@CachePut`      | 将方法的返回值放到缓存中                                     |
| `@CacheEvict`    | 将一条或多条数据从缓存中删除                                 |

在spring boot项目中，使用缓存技术只需要在项目中导入相关缓存技术的依赖包，并在启动类上使用`@EnableCaching`开启缓存支持即可。

例如，使用Redis作为缓存技术，只需要导入Spring data Redis的maven坐标即可。

### 5.3 Spring Cache 使用方式

在Spring boot项目中使用Spring Cache的步骤（使用Redis缓存技术）：

1. 导入maven坐标

   Spring-boot-starter-data-redis, spring-boot-starter-cache

2. 配置`application.yml`

   ```yml
   spring:
   	cache:
   		redis:
   			time-to-live: 1800000
   ```

3. 在启动类上加入`@EnableCaching`注解，开启缓存注解功能

4. 在Controller方法上加入`@Cacheable`、`@CacheEvict`等注解，进行缓存操作



## 6. 缓存套餐数据


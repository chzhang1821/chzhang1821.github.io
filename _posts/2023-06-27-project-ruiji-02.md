---
layout:     post
title:      "项目实战--瑞吉外卖Day02"
subtitle:   
date:       2023-06-27 16:00
author:     "Chenghao"
header-img: "img/post-bg-2015.jpg"
tags:
    - Spring
    - Springboot
    - project
---

> 笔记摘自：黑马程序员 | B站视频链接：<https://www.bilibili.com/video/BV13a411q753/>

# 员工管理业务开发

## 1. 效果展示

<img src = "https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/ruiji_project_imgs/员工管理效果展示.png?raw=true" alt = "员工管理效果展示" style="zoom:60%"/>



## 2. 完善登录功能

### 2.1 问题分析

存在问题：用户如果不登录，直接访问系统首页，照样可以正常访问

这种设计不合理，我们希望必须通过登录页面完成登录才能访问首页，否则如果直接访问首页，则应跳转到登录页面

实现方法：过滤器或拦截器，在过滤器或拦截器中判断用户是否已经完成登录，如果没有则跳转回登录页面



### 2.2 代码实现

实现步骤

1. 创建自定义过滤器`LoginCheckFilter`
2. 在启动类上加入注解`@ServletComponentScan`
3. 完善过滤器的处理逻辑
   1. 获取本次请求的URI
   2. 判断本次请求是否需要处理
   3. 如果不需要处理，则直接放行
   4. 判断登录状态，如果已登录，则直接放行
   5. 如果未登录则返回未登录结果

<img src = "https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/ruiji_project_imgs/完善登录-过滤器处理逻辑.png?raw=true" alt = "完善登录-过滤器处理逻辑" style="zoom:50%"/>





### 2.3 功能测试



## 3. 新增员工

### 3.1 需求分析

后台系统中可以管理员工信息，通过新增员工来添加后台系统用户。点击[添加员工]按钮跳转到新增页面，如下：

<img src = "https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/ruiji_project_imgs/新增员工.png?raw=true" alt = "新增员工" style="zoom:40%"/>

### 3.2 数据模型

新增员工，其实就是将我们新增页面录入的员工数据插入到employee表。需注意，employee表中对username字段加入了唯一约束，因为username是员工的登录账号，必须是唯一的。

employee表中的status字段已经设置了默认值1，表示状态正常。

### 3.3 代码开发

程序执行过程：

1. 页面发送ajax请求，将新增员工页面中输入的数据以json格式提交到服务器
2. 服务端Controller接受页面提交的数据并调用Service将数据进行保存
3. Service调用Mapper操作数据库，保存数据

<img src = "https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/ruiji_project_imgs/新增员工示例.png?raw=true" alt = "新增员工示例" style="zoom:40%"/>

```java
@PostMapping
public R<String> save(HttpServletRequest request, @RequestBody Employee employee) {
    log.info("新增员工，员工信息：{}", employee.toString());
    // 设置初始密码：123456，需进行md5加密
    employee.setPassword(DigestUtils.md5DigestAsHex("123456".getBytes()));
    employee.setCreateTime(LocalDateTime.now());
    employee.setUpdateTime(LocalDateTime.now());

    Long empId = (Long) request.getSession().getAttribute("employee");
    employee.setCreateUser(empId);
    employee.setUpdateUser(empId);

    employeeService.save(employee);
    return R.sucess("新增员工成功");
}
```

上述程序还存在一个问题，就是当我们在新增员工时输入的账号已经存在，由于employee表中对该字段加入了唯一约束，此时程序会抛出异常：

`java.sql.SQLIntegrityConstraintViolationException: Duplicate entry 'zhangsan' for key 'employee.idx_username'`

此时需要我们的程序进行异常捕获

1. 在Controller方法中加入`try ... catch` 进行异常捕获 - 不建议，如果需要保存的信息很多，则`try ... catch ...`代码块过多
2. 使用异常处理器进行全局异常捕获

```java
/**
 * 全局异常处理
 */
@ControllerAdvice(annotations = {RestController.class, Controller.class})
@ResponseBody
@Slf4j
public class GlobalExceptionHandler {

    /**
     * 异常方法处理
     * @param exception
     * @return
     */
    @ExceptionHandler(SQLIntegrityConstraintViolationException.class)
    public R<String> exceptionHandler(SQLIntegrityConstraintViolationException exception) {
        log.error(exception.getMessage());
        if (exception.getMessage().contains("Duplicate entry")) {
            String[] split = exception.getMessage().split(" ");
            String msg = split[2] + "已存在";
            return R.error(msg);
        }
        return R.error("未知错误");
    }
}
```

### 3.4 功能测试



## 4. 员工信息分页查询

### 4.1 需求分析

系统中的员工很多时，如果在一个页面中全部展示出来会显得比较乱，不便查看，所以一般的系统中都会以分页的方式来展示列表数据

<img src = "https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/ruiji_project_imgs/分页展示.png?raw=true" alt = "分页展示" style="zoom:40%"/>

### 4.2 代码开发

程序执行过程：

1. 页面发送ajax请求，将分页查询参数(page, pageSize, name)提交到服务器
2. 服务端Controller接收页面提交的数据并调用Service查询数据
3. Service调用Mapper操作数据库，查询分页数据
4. Controller将查询到的分页数据先应给页面
5. 页面接受到分页数据并通过ElementUI的Table组件展示到页面上

```java
@Configuration
public class MybatisPlusConfig {

    @Bean
    public MybatisPlusInterceptor mybatisPlusInterceptor() {
        MybatisPlusInterceptor mybatisPlusInterceptor = new MybatisPlusInterceptor();
        mybatisPlusInterceptor.addInnerInterceptor(new PaginationInnerInterceptor());
        return mybatisPlusInterceptor;
    }
}
```

```java
/**
 * 员工信息分页查询
 * @param page
 * @param pageSize
 * @param name
 * @return
 */
@GetMapping("/page")
public R<Page> page(int page, int pageSize, String name) {
    log.info("page = {}, pageSize = {}, name = {}", page, pageSize, name);
    // 构造分页构造器
    Page pageInfo = new Page(page, pageSize);

    // 构造条件构造器
    LambdaQueryWrapper<Employee> lambdaQueryWrapper = new LambdaQueryWrapper();
    // 添加过滤条件
    lambdaQueryWrapper.like(StringUtils.isNotEmpty(name), Employee::getName, name);
    // 添加排序条件
    lambdaQueryWrapper.orderByDesc(Employee::getUpdateTime);

    // 执行查询
    employeeService.page(pageInfo, lambdaQueryWrapper);
    return R.sucess(pageInfo);
}
```



## 5. 启用/禁用员工账号

### 5.1 需求分析

在员工管理列表页面，可以对某个员工账号进行启用或者禁用操作。账号禁用的员工不能登录系统，启用后的员工可以正常登录。

需要注意，只有管理员（admin用户）可以对其他普通用户进行启用、禁用操作，所以普通用户登录系统后，启用禁用操作不显示

### 5.2 代码开发

前端如何做到只有管理员admin能够看到启用、禁用按钮

<img src = "https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/ruiji_project_imgs/启用禁用按钮前端.png?raw=true" alt = "启用禁用按钮前端" style="zoom:40%"/>

程序执行过程：

1. 页面发送ajax请求，将参数（id， status）提交到服务端

<img src = "https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/ruiji_project_imgs/启用禁用按钮如何发送ajax请求.png?raw=true" alt = "启用禁用按钮如何发送ajax请求" style="zoom:40%"/>

1. 服务端Controller接收页面提交的数据并调用Service更新数据
2. Service调用Mapper操作数据库

### 5.3 功能测试

测试过程中没有报错，但功能并没有实现，查看数据库中的数据也没有变化

控制台输出的SQL：

```sql
==>  Preparing: UPDATE employee SET status=?, update_time=?, update_user=? WHERE id=?
==> Parameters: 0(Integer), 2023-07-01T13:48:44.418200(LocalDateTime), 1(Long), 1674988663483748400(Long)
<==    Updates: 0
```

SQL执行的结果是更新的行数为0，仔细观察id的值，和数据库中对应记录的id值并不相同

<img src = "https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/ruiji_project_imgs/启用禁用功能测试.png?raw=true" alt = "启用禁用功能测试" style="zoom:40%"/>

出现该问题的原因是因为js对Long类型的数据进行处理时丢失精度，倒置提交的id和数据库中的id不一致。

我们可以在服务端给页面响应json数据时进行处理，将long类型数据统一转为String字符串



**代码修复**

1. 提供对象转换器`JacksonBojectMapper`，基于Jackson进行java对象到json数据的转换

```java
/**
 * 对象映射器：基于jackson将Java对象转为json，或者讲json转为Java对象
 * 将JSON解析为Java对象的过程称为 [从JSON反序列化Java对象]
 * 从Java对象生成JSON的过程陈伟 [序列化Java对象到JSON]
 */
@Component
public class JacksonObjectMapper extends ObjectMapper {
    public static final String DEFAULT_DATE_FORMAT = "yyyy-MM-dd";
    public static final String DEFAULT_DATE_TIME_FORMAT = "yyyy-MM-dd HH:mm:ss";
    public static final String DEFAULT_TIME_FORMAT = "HH:mm:ss";

    public JacksonObjectMapper() {
        super();
        // 收到未知属性时不报异常
        this.configure(DeserializationFeature.FAIL_ON_UNKNOWN_PROPERTIES, false);
        // 反序列化时，属性不存在的兼容处理
        this.getDeserializationConfig().withoutFeatures(DeserializationFeature.FAIL_ON_UNKNOWN_PROPERTIES);

        SimpleModule simpleModule = new SimpleModule()
                .addDeserializer(LocalDateTime.class, new LocalDateTimeDeserializer(DateTimeFormatter.ofPattern(DEFAULT_DATE_TIME_FORMAT)))
                .addDeserializer(LocalDate.class, new LocalDateDeserializer(DateTimeFormatter.ofPattern(DEFAULT_DATE_FORMAT)))
                .addDeserializer(LocalTime.class, new LocalTimeDeserializer(DateTimeFormatter.ofPattern(DEFAULT_TIME_FORMAT)))

                .addSerializer(BigInteger.class, ToStringSerializer.instance)
                .addSerializer(Long.class, ToStringSerializer.instance)
                .addSerializer(LocalDateTime.class, new LocalDateTimeSerializer(DateTimeFormatter.ofPattern(DEFAULT_DATE_TIME_FORMAT)))
                .addSerializer(LocalDate.class, new LocalDateSerializer(DateTimeFormatter.ofPattern(DEFAULT_DATE_FORMAT)))
                .addSerializer(LocalTime.class, new LocalTimeSerializer(DateTimeFormatter.ofPattern(DEFAULT_TIME_FORMAT)));

        // 注册功能模块 例如，可以添加自定义序列化器和反序列化器
        this.registerModule(simpleModule);
    }
}
```



2. 在`WebMvcConfig`配置类中扩展Spring mvc的消息转换器，再此消息转换器中使用提供的对象转换器进行java对象到json数据的转换

```java
@Slf4j
@Configuration
public class WebMvcConfig implements WebMvcConfigurer {
//    /**
//     * 设置静态资源映射
//     * @param registry
//     */
//    @Override
//    protected void addResourceHandlers(ResourceHandlerRegistry registry) {
//        log.info("开始进行金泰资源映射...");
//        registry.addResourceHandler("/backened/**").addResourceLocations("classpath:/backend/");
//        registry.addResourceHandler("/front/**").addResourceLocations("classpath:/front/");
//    }

    @Override
    public void extendMessageConverters(List<HttpMessageConverter<?>> converters) {
        log.info("扩展消息转换器");
        // 创建消息转换器对象
        MappingJackson2HttpMessageConverter messageConverter = new MappingJackson2HttpMessageConverter();
        // 设置对象转换器，底层使用Jackson将Java对象转为json
        messageConverter.setObjectMapper(new JacksonObjectMapper());
        // 将上面的消息转换器对象追加到mvc框架的转换容器集合中
        converters.add(0, messageConverter);

    }
}
```



### 5.4 需求分析



## 6. 编辑员工信息

### 6.1 需求分析

在员工管理列表页面点击编辑按钮，跳转到编辑页面，在编辑页面辉县员工信息并进行修改，最后点击保存按钮完成编辑

### 6.2 代码开发

流程：

1. 点击编辑按钮时，页面跳转到add.html，并在url中鞋带参数[员工id]
2. 在add.html页面获取url中的参数[员工id]
3. 发送ajax请求，请求服务端，同时提交员工id参数
4. 服务端接受请求，根据员工id查询员工信息，将员工信息以json形式响应给页面
5. 页面接受服务端响应的json数据，通过vue的数据绑定进行员工信息回显
6. 点击保存按钮，发送ajax请求，将页面的员工信息以json方式提交给服务端
7. 服务端接受员工信息，并进行处理，完成后给页面响应
8. 页面接收到服务端相应信息后进行相应处理

> add.html页面为公共页面，新增员工和编辑员工信息都是在此页面操作

```java
@GetMapping("/{id}")
public R<Employee> getById(@PathVariable Long id) {
    log.info("根据ID查询员工信息");
    Employee employee = employeeService.getById(id);
    if (employee != null) {
        return R.success(employee);
    }
    return R.error("没有查询到对应员工信息");
}
```



### 6.3 功能测试


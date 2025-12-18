---
title: SpringBoot
date: 2024/08/27
tags:
  - Spring全家桶
categories:
  - 后端
---

# Spring Boot 简介

Spring Boot 是由 Pivotal 团队提供的全新框架，其设计目的是简化新 Spring 应用的初始搭建以及开发过程。该框架使用了预设默认配置的方式，使开发人员不再需要定义繁琐的
XML 配置文件，从而快速地创建独立的、生产级别的基于 Spring 框架的应用程序。

## 核心特性

1. **自动配置**：根据添加的 jar 依赖自动配置 Spring 应用。
2. **起步依赖**：通过 `spring-boot-starter-*` 项目简化 Maven 或 Gradle 配置。
3. **独立运行**：内置 Tomcat、Jetty 或 Undertow 容器，可以独立运行。
4. **生产就绪**：提供生产环境的监控和管理功能。
5. **无代码生成**：不需要生成代码，也不需要 XML 配置。

## SpringBoot常用注解

* 组件扫描与配置
    * @SpringBootApplication：组合了 @Configuration、@EnableAutoConfiguration 和 @ComponentScan 注解，用于启动 Spring Boot
      应用。
    * @ComponentScan：指定扫描哪些包下的组件，默认扫描当前类所在的包及其子包。
    * @Configuration：标记一个类为配置类，可以替代 XML 配置文件。
* 依赖注入
    * @Autowired：自动装配依赖，可以用于字段、构造器或方法上。
    * @Qualifier：当有多个相同类型的 Bean 时，用于指定注入哪个 Bean。
    * @Value：从配置文件中读取值并注入到字段中。
* 控制器与 RESTful API
    * @Controller：标记一个类为控制器，通常用于处理 Web 请求。
    * @RestController：组合了 @Controller 和 @ResponseBody，用于构建 RESTful API。
    * @RequestMapping：映射 HTTP 请求到处理方法上。
    * @GetMapping、@PostMapping、@PutMapping、@DeleteMapping：分别映射 GET、POST、PUT、DELETE 请求。
* 数据访问
    * @Mapper：标记一个类为数据访问层组件，通常用于 DAO 类。
    * @Transactional：管理事务，可以应用于方法或类上。
* 服务层
    * @Service：标记一个类为服务层组件，通常用于业务逻辑处理。
* 配置属性
    * @ConfigurationProperties：绑定配置文件中的属性到 Java 对象中。
    * @PropertySource：指定配置文件的位置。
* 异常处理
    * @ExceptionHandler：处理特定类型的异常。
    * @RestControllerAdvice：全局异常处理，可以应用于多个控制器。
* 测试
    * @SpringBootTest：用于启动 Spring Boot 应用的集成测试。
    * @WebMvcTest：用于测试 Spring MVC 控制器。
    * @DataJpaTest：用于测试 JPA 相关的功能。

## SpringBoot常用的自动装配 Maven 依赖

1. Web 开发

    * spring-boot-starter-web: 包含 Spring MVC 和嵌入式 Tomcat。
    * spring-boot-starter-thymeleaf: Thymeleaf 模板引擎支持。
    * spring-boot-starter-freemarker: Freemarker 模板引擎支持。

2. 数据访问

    * spring-boot-starter-data-jpa: JPA 支持，通常与 Hibernate 配合使用。
    * spring-boot-starter-data-mongodb: MongoDB 数据库支持。
    * spring-boot-starter-data-redis: Redis 数据库支持。
    * spring-boot-starter-jdbc: JDBC 支持，通常与 HikariCP 连接池配合使用。

3. 安全

    * spring-boot-starter-security: Spring Security 安全框架支持。

4. 消息队列

    * spring-boot-starter-amqp: AMQP 协议支持，通常与 RabbitMQ 配合使用。
    * spring-boot-starter-kafka: Apache Kafka 支持。

5. 缓存

    * spring-boot-starter-cache: 缓存支持，通常与 Ehcache 或 Caffeine 配合使用。

6. 日志

    * spring-boot-starter-logging: 默认使用 Logback 日志框架。
    * log4j-spring-boot-starter: 使用 Log4j2 日志框架。

7. 监控

    * spring-boot-starter-actuator: 提供应用监控和管理功能。

8. 测试

    * spring-boot-starter-test: 包含 JUnit、Mockito 等测试工具。

## 快速入门

### 创建 Spring Boot 项目

1. **使用 Spring Initializr**：
    - 访问 [Spring Initializr](https://start.spring.io/)
    - 选择项目类型（Maven 或 Gradle）、语言（Java、Kotlin 等）
    - 添加依赖（如 Spring Web、Spring Data JPA 等）
    - 生成并下载项目

2. **手动创建**：
    - 创建 Maven 或 Gradle 项目
    - 添加 Spring Boot 依赖

## 项目结构

```html
├── main
│ ├── java
│ │ └── com.example
│ │ └── DemoApplication.java
│ └── resources
│ ├── application.properties
│ └── static
│ └── templates
└── test
└── java
└── com.example
└── DemoApplicationTests.java
```

## 示例

### 主启动类

```java
package com.example;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class DemoApplication {
    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }
}
```

### 控制层

```java
package com.zjjhy.controller.common;

import com.zjjhy.pojo.vo.Result;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class WebController {

    /**
     * 基础接口
     *
     * @return
     */
    @GetMapping("/")
    public Result hello() {
        return Result.success("访问成功");
    }
}
```

### 业务层

```java
package com.example.service;

import com.example.entity.User;
import com.example.repository.UserRepository;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.List;

@Service
public class UserService {
    @Autowired
    private OperateLogMapper operateLogMapper;

    public void findAll() {
        return OperateLogMapper.insertLog();
    }
}
```

### 持久层

```java
package com.zjjhy.mapper;

import com.zjjhy.pojo.entity.OperateLog;
import org.apache.ibatis.annotations.Insert;
import org.apache.ibatis.annotations.Mapper;

@Mapper
public interface OperateLogMapper {

    /**
     * 插入日志
     */
    @Insert("insert into operate_log(operate_user_id,operate_time,class_name,method_name,method_params,return_value,cost_time)" +
            "values(#{operateUserId},#{operateTime},#{className},#{methodName},#{methodParams},#{returnValue},#{costTime})")
    void insertLog(OperateLog operateLog);
}

```

### 配置文件

1. `application.properties` 文件位于 `src/main/resources` 目录下，用于配置应用的各种属性。

```properties
spring.application.name=springboot
spring.datasource.url=jdbc:mysql://localhost:3306/数据库名?useSSL=false&serverTimezone=UTC
spring.datasource.username=用户名
spring.datasource.password=密码
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
```

2. `application.yml` 文件位于 `src/main/resources` 目录下，用于配置应用的各种属性。

```yaml
server:
  port: 8088

spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/blog?useUnicode=true&characterEncoding=utf-8&allowMultiQueries=true&useSSL=false&serverTimezone=GMT%2b8&allowPublicKeyRetrieval=true
    username: root
    password: root
  servlet:
    multipart:
      #单个文件大小
      max-file-size: 100MB
      #总文件上传的大小
      max-request-size: 100MB

mybatis:
  mapper-locations: classpath:mapper/*.xml
  type-aliases-package: com.zjjhy.entity
  configuration:
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
    map-underscore-to-camel-case: true

ip: localhost
```

## @Configuration 注解的使用场景

### 在 Spring Boot 中，@Configuration 注解用于标记一个类为配置类。配置类的主要作用是定义和管理 Bean。以下是常见的几种需要使用@Configuration 注解的场景：

1. **定义 Bean：**

* 当你需要在一个类中定义多个 @Bean 方法时，应该使用 @Configuration 注解。这些 @Bean 方法用于创建和管理 Spring 容器中的
  Bean。
* 示例：

```java

@Configuration
public class AppConfig {
    @Bean
    public MyService myService() {
        return new MyServiceImpl();
    }

    @Bean
    public MyRepository myRepository() {
        return new MyRepositoryImpl();
    }
}
```

2. **配置属性：**

* 当你需要配置一些属性或设置一些全局配置时，可以使用 @Configuration 注解。
* 示例：

```java

@Configuration
public class WebConfig implements WebMvcConfigurer {
    @Override
    public void addViewControllers(ViewControllerRegistry registry) {
        registry.addViewController("/home").setViewName("home");
    }
}
```

3. **集成第三方库：**

* 当你需要集成第三方库并配置其 Bean 时，可以使用 @Configuration 注解。
* 示例：

```java

@Configuration
public class RedisConfig {
    @Bean
    public RedisConnectionFactory redisConnectionFactory() {
        JedisConnectionFactory jedisConnectionFactory = new JedisConnectionFactory();
        jedisConnectionFactory.setHostName("localhost");
        jedisConnectionFactory.setPort(6379);
        return jedisConnectionFactory;
    }

    @Bean
    public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory factory) {
        RedisTemplate<String, Object> template = new RedisTemplate<>();
        template.setConnectionFactory(factory);
        template.setKeySerializer(new StringRedisSerializer());
        template.setValueSerializer(new StringRedisSerializer());
        return template;
    }
}
```

4. **条件配置：**

* 当你需要根据某些条件动态地配置 Bean 时，可以使用 @Configuration 注解结合 @Conditional 注解。
* 示例：

```java

@Configuration
@ConditionalOnProperty(name = "feature.enabled", havingValue = "true")
public class FeatureConfig {
    @Bean
    public FeatureService featureService() {
        return new FeatureServiceImpl();
    }
}
```

5. **配置文件解析：**

* 当你需要从配置文件中读取属性并将其注入到 Bean 中时，可以使用 @Configuration 注解结合 @Value 或 @ConfigurationProperties
  注解。
* 示例：

```java

@Configuration
public class DatabaseConfig {
    @Value("${db.url}")
    private String dbUrl;

    @Bean
    public DataSource dataSource() {
        DriverManagerDataSource dataSource = new DriverManagerDataSource();
        dataSource.setUrl(dbUrl);
        return dataSource;
    }
}
```

* **总结**
* @Configuration 注解主要用于标记配置类，这些类通常包含 @Bean 方法，用于定义和管理 Spring 容器中的 Bean。如果你需要在一个类中定义多个
  Bean 或进行复杂的配置，建议使用 @Configuration 注解。

## @Transactional事务注解

### 通常有以下几种放置位置：

1. 方法级别
   最常见的是将 @Transactional 注解放在需要事务管理的方法上。这样可以确保该方法在事务中执行，如果方法抛出异常，事务会自动回滚。

2. 类级别
   将 @Transactional 注解放在类级别上，可以使得该类中的所有公共方法都默认在事务中执行。这种方式适用于大多数方法都需要事务管理的场景。

3. 接口级别
   虽然不常见，但也可以将 @Transactional 注解放在接口上。这种方式会使实现该接口的所有方法都默认在事务中执行。

### 主要属性及其说明：

1. value 或 transactionManager

* 类型: String
* 默认值: ""
* 说明: 指定要使用的事务管理器的名称。如果只有一个事务管理器，通常不需要指定此属性。

2. propagation

* 类型: Propagation
* 默认值: Propagation.REQUIRED
* 说明: 定义事务的传播行为。常见的值包括：
    * REQUIRED: 如果当前存在事务，则加入该事务；否则创建一个新的事务。
    * REQUIRES_NEW: 创建一个新的事务，如果当前存在事务，则暂停当前事务。
    * SUPPORTS: 如果当前存在事务，则加入该事务；否则以非事务方式执行。
    * NOT_SUPPORTED: 以非事务方式执行，如果当前存在事务，则暂停当前事务。
    * MANDATORY: 如果当前存在事务，则加入该事务；否则抛出异常。
    * NEVER: 以非事务方式执行，如果当前存在事务，则抛出异常。
    * NESTED: 如果当前存在事务，则在嵌套事务内执行；否则创建一个新的事务。

3. isolation

* 类型: Isolation
* 默认值: Isolation.DEFAULT
* 说明: 定义事务的隔离级别。常见的值包括：
    * DEFAULT: 使用数据库的默认隔离级别。
    * READ_UNCOMMITTED: 允许读取未提交的数据。
    * READ_COMMITTED: 只允许读取已提交的数据。
    * REPEATABLE_READ: 在同一事务中多次读取同一数据返回相同的结果。
    * SERIALIZABLE: 最高的隔离级别，完全防止脏读、不可重复读和幻读。

4. timeout

* 类型: int
* 默认值: -1
* 说明: 定义事务的超时时间（以秒为单位）。如果设置为 -1，则表示使用默认的超时时间。

5. readOnly

* 类型: boolean
* 默认值: false
* 说明: 指定事务是否为只读事务。只读事务可以提高性能，因为它不需要进行写操作的锁定。

6. rollbackFor

* 类型: Class<?>[]
* 默认值: {}（空数组）
* 说明: 指定哪些异常会导致事务回滚。可以指定一个或多个异常类。

7. noRollbackFor

* 类型: Class<?>[]
* 默认值: {}（空数组）
* 说明: 指定哪些异常不会导致事务回滚。可以指定一个或多个异常类。

* **示例代码**

```java

@Service
public class AdminSystemUserServiceImpl implements AdminSystemUserService {

    @Autowired
    private AdminSystemUserMapper adminSystemUserMapper;

    @Autowired
    private AdminSystemHomeMapper adminSystemHomeMapper;

    @Transactional(
            propagation = Propagation.REQUIRED,
            isolation = Isolation.READ_COMMITTED,
            timeout = 30,
            readOnly = false,
            rollbackFor = {Exception.class},
            noRollbackFor = {MyCustomException.class}
    )
    @Override
    public void deleteUser(List<Integer> ids) {
        int i1 = adminSystemUserMapper.deleteUser(ids);
        int i2 = adminSystemHomeMapper.deleteDocsByUserId(ids);
        if (i1 == 0 || i2 == 0) {
            throw new BusinessException(ResultCodeEnum.SYSTEM_ERROR);
        }
    }
}

```

### 应用场景

1. **数据库操作组合**
   当一个业务逻辑涉及多个数据库操作（如插入、更新、删除等），并且这些操作必须作为一个整体成功或失败时，需要使用事务管理。例如：

```java

@Transactional
public void addUserAndLog(UserDto userDto) {
    // 插入用户信息
    userRepository.save(userDto.toEntity());

    // 记录日志
    logRepository.save(new Log("User added: " + userDto.getUsername()));
}

```

2. 跨服务调用
   当一个服务调用另一个服务的方法，并且这两个方法都需要在一个事务中执行时，可以使用事务管理。例如：

```java

@Transactional
public void processOrder(Order order) {
    // 创建订单
    orderService.createOrder(order);

    // 更新库存
    inventoryService.updateInventory(order.getProductId(), order.getQuantity());
}

```

3. 数据一致性
   当需要确保多个表的数据一致性时，事务管理可以防止部分操作成功而部分操作失败的情况。例如：

```java

@Transactional
public void transferMoney(Account fromAccount, Account toAccount, double amount) {
    // 从一个账户扣款
    fromAccount.setBalance(fromAccount.getBalance() - amount);
    accountRepository.save(fromAccount);

    // 向另一个账户存款
    toAccount.setBalance(toAccount.getBalance() + amount);
    accountRepository.save(toAccount);
}

```

4. 批量操作
   当需要批量插入、更新或删除数据时，事务管理可以确保所有操作要么全部成功，要么全部失败。例如：

```java

@Transactional
public void batchUpdateUsers(List<UserDto> users) {
    for (UserDto userDto : users) {
        User user = userRepository.findById(userDto.getId()).orElseThrow(() -> new RuntimeException("User not found"));
        user.setUsername(userDto.getUsername());
        user.setEmail(userDto.getEmail());
        userRepository.save(user);
    }
}

```

5. 异常处理
   当某个操作抛出异常时，事务管理可以确保回滚所有已经执行的操作，从而保持数据的一致性。例如：

```java

@Transactional
public void createUserWithValidation(UserDto userDto) {
    // 验证用户信息
    validateUser(userDto);

    // 创建用户
    userRepository.save(userDto.toEntity());
}

```

6. 读取和写入组合
   当一个业务逻辑需要先读取数据，然后基于读取的数据进行写入操作时，事务管理可以确保读取和写入操作的一致性。例如：

```java

@Transactional
public void updateStock(Product product, int quantity) {
    // 读取当前库存
    int currentStock = stockRepository.findStockByProductId(product.getId());

    // 更新库存
    currentStock -= quantity;
    stockRepository.updateStock(product.getId(), currentStock);
}

```

**总结：** Spring Boot 的事务管理通过 @Transactional 注解来实现，它可以应用于方法或类级别。事务管理确保了多个数据库操作的原子性、一致性、隔离性和持久性（ACID
特性），从而保证了数据的完整性和一致性。

## 使用过滤器实现跨域配置类

```java
package com.zjjhy.common.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.cors.CorsConfiguration;
import org.springframework.web.cors.UrlBasedCorsConfigurationSource;
import org.springframework.web.filter.CorsFilter;

/**
 * 跨域配置
 */
@Configuration
public class CorsConfig {

    @Bean
    public CorsFilter corsFilter() {
        UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource();
        CorsConfiguration corsConfiguration = new CorsConfiguration();
        corsConfiguration.addAllowedOrigin("*"); // 1 设置访问源地址
        corsConfiguration.addAllowedHeader("*"); // 2 设置访问源请求头
        corsConfiguration.addAllowedMethod("*"); // 3 设置访问源请求方法
        source.registerCorsConfiguration("/**", corsConfiguration); // 4 对接口配置跨域设置
        return new CorsFilter(source);
    }
}
```

## 全局异常处理器

### 1.自定义业务异常继承RuntimeException（非检查性异常）

```java
package com.zjjhy.common.exception;

import com.zjjhy.common.enums.ResultCodeEnum;

public class BusinessException extends RuntimeException {
    private String code;

    public BusinessException(ResultCodeEnum codeEnum) {
        super(codeEnum.msg);
        this.code = codeEnum.code;
    }

    // 抛出当前异常对象的时候可以传递其他异常作为原因
    // 通过在异常构造函数中传递 Throwable 对象，可以实现链式异常，提供更详细的错误信息，有助于调试和问题定位。
    // 这种方式在处理复杂错误时非常有用，可以更好地追踪错误的来源。
    public BusinessException(ResultCodeEnum codeEnum, Throwable cause) {
        super(codeEnum.msg, cause);
        this.code = codeEnum.code;
    }

    public String getCode() {
        return code;
    }

    public void setCode(String code) {
        this.code = code;
    }
}
```

### 2.自定义系统异常继承RuntimeException（非检查性异常）

```java
package com.zjjhy.common.exception;

import com.zjjhy.common.enums.ResultCodeEnum;

public class SystemException extends RuntimeException {
    private String code;

    public SystemException(ResultCodeEnum codeEnum) {
        super(codeEnum.msg);
        this.code = codeEnum.code;
    }

    // 抛出当前异常对象的时候可以传递其他异常作为原因
    // 通过在异常构造函数中传递 Throwable 对象，可以实现链式异常，提供更详细的错误信息，有助于调试和问题定位。
    // 这种方式在处理复杂错误时非常有用，可以更好地追踪错误的来源。
    public SystemException(ResultCodeEnum codeEnum, Throwable cause) {
        super(codeEnum.msg, cause);
        this.code = codeEnum.code;
    }

    public String getCode() {
        return code;
    }

    public void setCode(String code) {
        this.code = code;
    }
}

```

### 3.新建全局异常处理类，用于统一处理项目中抛出的异常(非检查性异常，编译器不会强制处理，所以异常会一直往上抛直到被全局异常处理类捕获并分发到对应方法统一处理异常)

```java
package com.zjjhy.common.exception;

import com.zjjhy.common.enums.ResultCodeEnum;
import com.zjjhy.pojo.vo.Result;
import lombok.extern.slf4j.Slf4j;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.RestControllerAdvice;

@RestControllerAdvice
@Slf4j
public class GlobalExceptionHandler {

    @ExceptionHandler(SystemException.class)
    public Result doSystemException(SystemException se) {
        //没必要 调用 getCause 异常原因就只有当前这个异常，没有其他异常原因
        log.error("系统异常:code={},msg={}", se.getCode(), se.getMessage());
        return Result.error(se.getCode(), se.getMessage());
    }

    @ExceptionHandler(BusinessException.class)
    public Result doBusinessException(BusinessException be) {
        log.error("业务异常:code={},msg={}", be.getCode(), be.getMessage());
        return Result.error(be.getCode(), be.getMessage());
    }

    @ExceptionHandler(Exception.class)
    public Result doException(Exception ex) {
        // getCause 方法是 Throwable 类中的一个方法，用于获取当前异常的原因
        log.error("异常:msg={}", ex.getMessage(), ex);
        return Result.error(ResultCodeEnum.SYSTEM_ERROR);
    }
}

```

## ThreadLocal实现存储用户当前用户唯一信息（id）

### 1.创建上下文类

```java
package com.zjjhy.common.context;

import lombok.extern.slf4j.Slf4j;

/**
 * BaseContext类用于存储和管理每个请求的唯一标识符
 * 它使用ThreadLocal来确保每个线程都有其独立的请求标识符副本，从而避免并发问题
 */
@Slf4j
public class BaseContext {
    // 使用ThreadLocal存储每个线程独有的请求标识符
    public static ThreadLocal<Long> threadLocal = new ThreadLocal<>();

    public static void setCurrentId(Long id) {
        log.info("当前线程本地用户id--->" + id);
        threadLocal.set(id);
    }

    public static Long getCurrent() {
        return threadLocal.get();
    }

    public static void removeCurrentId() {
        threadLocal.remove();
    }
}
```

### 2.每次token令牌解析的时候将当前用户信息set进去

```java
public static boolean parseToken(String token) {
    log.info("jwt校验：{}", token);
    //解析token获取存储的载荷 id和role
    String userRole = JWT.decode(token).getAudience().get(0);
    String userId = userRole.split("-")[0];  // 获取用户id --> 1
    log.info("当前用户id：{}", userId);
    //在springboot应用中，每个http请求都是有servlet容器处理，这些容器通常使用线程池来处理请求
    //因此，每个请求可能会分配到线程池中的一个线程来处理，而不是每次创建新的线程
    BaseContext.setCurrentId(Long.parseLong(userId));
    String role = userRole.split("-")[1];    // 获取角色 --> 管理员
}
```

### 总结

* 在这个案例中，属于是存一下拿一下，可以说是能确保id不会指向null，能不能复用线程有待验证

## 使用aop和自定义注解实现公共字段注入案例

### 1.创建自定义注解

```java
package com.zjjhy.common.annotation;

import com.zjjhy.common.enums.OperationTypeEnum;

import java.lang.annotation.*;

/**
 * 自定义注解,用于标识某个方法需要进行功能字段自动填充处理
 */
@Target(ElementType.METHOD) //(元注解)指定注解可以用于方法上
@Retention(RetentionPolicy.RUNTIME) //（元注解）指定注解在运行时保留
@Documented //（元注解）指定注解包含在 JavaDoc 中
public @interface AutoFill {
    // 数据库操作类型, UPDATE, INSERT 
    // 没有默认值的话使用该注解的时候必须传对应的返回值类型
    // 设置默认值例:OperationTypeEnum value() default OperationTypeEnum.INSERT;
    OperationTypeEnum value();
}


```

* 为什么要用方法签名的方式定义成员属性
    1. 语法一致性：注解的成员属性以方法的形式定义，使得注解的语法与其他 Java 语法保持一致。这样可以减少学习成本，使开发者更容易理解和使用注解。
    2. 默认值支持：通过方法形式定义成员属性，可以方便地为属性设置默认值。例如：
    ```java
        public @interface MyAnnotation {
           String value() default "default value";
         }
    ```
    3. 类型安全：方法形式的成员属性可以明确指定其返回类型，从而确保类型安全。例如，OperationTypeEnum value(); 明确指定了
       value 属性的类型是 OperationTypeEnum。
    4. 访问方式：通过方法形式定义的成员属性，可以在使用注解时以键值对的形式赋值。例如：
    ```java
        @AutoFill(value = OperationTypeEnum.INSERT)
        public void insertData() {
           // 插入数据的逻辑
        }
   ```
    5. 反射机制：在运行时，可以通过反射机制获取注解的成员属性值。反射 API 会将注解的成员属性视为方法来调用。例如：
    ```java
        AutoFill autoFill = MyService.class.getMethod("insertData").getAnnotation(AutoFill.class);
        OperationTypeEnum operationType = autoFill.value();
   ```

### 2.创建aop切面类

```java
package com.zjjhy.common.aspect;

import cn.hutool.core.util.ObjectUtil;
import com.zjjhy.common.annotation.AutoFill;
import com.zjjhy.common.enums.MethodEnum;
import com.zjjhy.common.enums.OperationTypeEnum;
import lombok.extern.slf4j.Slf4j;
import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.aspectj.lang.annotation.Pointcut;
import org.aspectj.lang.reflect.MethodSignature;
import org.springframework.stereotype.Component;

import java.lang.reflect.Method;
import java.time.LocalDateTime;

/**
 * 自定义切面,实现公共字段自动填充处理逻辑
 */
@Aspect
@Component
@Slf4j
public class AutoFillAspect {

    /**
     * 注解切入点
     */
    @Pointcut("@annotation(com.zjjhy.common.annotation.AutoFill)")
    public void autoFillPointCut() {
    }

    /**
     * 前置通知,在通知中进行公共字段的赋值
     */
    @Before("autoFillPointCut()")
    public void autoFill(JoinPoint joinPoint) {
        log.info("开始进行公共字段的填充...");

        //获取到当前被拦截的方法上的数据库操作类型
        MethodSignature signature = (MethodSignature) joinPoint.getSignature();//方法签名对象
        AutoFill autoFill = signature.getMethod().getAnnotation(AutoFill.class);//获得方法上的注解对象
        OperationTypeEnum operationTypeEnum = autoFill.value();//获得数据库操作类型

        //获取到当前被拦截的方法的参数 -- 实体对象
        Object[] args = joinPoint.getArgs();
        if (ObjectUtil.isEmpty(args)) {
            return;
        }

        //获取对象数组的第一个参数对象
        Object pojo = args[0];
        log.info("args:{}", args);

        //准备赋值的数据
        LocalDateTime now = LocalDateTime.now();

        //根据当前不同的操作类型,为对应的属性赋值通过反射赋值
        if (operationTypeEnum == OperationTypeEnum.INSERT) {
            try {
                //反射获取到方法形参对象的set方法，并指定方法的参数类型为LocalDateTime
                Method setCreateTime = pojo.getClass().getDeclaredMethod(MethodEnum.SET_CREATE_TIME.str, LocalDateTime.class);
                Method setUpdateTime = pojo.getClass().getDeclaredMethod(MethodEnum.SET_UPDATE_TIME.str, LocalDateTime.class);

                // 通过反射为对象属性赋值
                // 1.反射调用方法时，需要指定方法是在哪个对象实例上调用的，pojo就是这个对象的实例
                // 2.需要的参数，需要跟实体类中的属性类型一致
                setCreateTime.invoke(pojo, now);
                setUpdateTime.invoke(pojo, now);
            } catch (Exception e) {
                throw new RuntimeException(e);
            }
        } else if (operationTypeEnum == OperationTypeEnum.UPDATE) {
            try {
                //反射获取到方法形参对象上的set方法，并指定方法的参数类型为LocalDateTime
                Method setUpdateTime = pojo.getClass().getDeclaredMethod(MethodEnum.SET_UPDATE_TIME.str, LocalDateTime.class);

                // 通过反射为对象属性赋值
                // 1.反射调用方法时，需要指定方法是在哪个对象实例上调用的，pojo就是这个对象的实例
                // 2.需要的参数，需要跟实体类中的属性类型一致
                setUpdateTime.invoke(pojo, now);
            } catch (Exception e) {
                throw new RuntimeException(e);
            }
        }
    }
}
```

## 使用aop和自定义注解实现记录操作日志案例

### 1.创建自定义注解

```java
package com.zjjhy.common.annotation;

import java.lang.annotation.*;

/**
 * 自定义注解，记录日志
 */
@Target(ElementType.METHOD) //(元注解)指定注解可以用于方法上
@Retention(RetentionPolicy.RUNTIME) //（元注解）指定注解在运行时保留
@Documented //（元注解）指定注解包含在 JavaDoc 中
//只需要单纯记录日志不需要成员属性来判断做具体操作
public @interface Log {
}

```

### 2.创建aop切面类

```java
package com.zjjhy.common.aspect;

import cn.hutool.core.util.ObjectUtil;
import com.alibaba.fastjson.JSONObject;
import com.zjjhy.common.context.BaseContext;
import com.zjjhy.common.enums.ResultCodeEnum;
import com.zjjhy.common.exception.BusinessException;
import com.zjjhy.mapper.OperateLogMapper;
import com.zjjhy.pojo.entity.OperateLog;
import lombok.extern.slf4j.Slf4j;
import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;
import org.mybatis.spring.MyBatisSystemException;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

import java.time.LocalDateTime;
import java.util.Arrays;

/**
 * 记录增删改日志，注意不要给文件上传加，因为没有用axios设置统一请求格式，没有token
 */
@Slf4j
@Component
@Aspect
public class LogAspect {

    @Autowired
    private OperateLogMapper operateLogMapper;

    /**
     * 环绕通知可以手动书写原始方法的调用，并拿到返回值
     * 环绕通知依赖形参ProceedingJoinPoint才能实现对原始方法的调用
     * 环绕通知可以隔离原始方法的调用执行
     *
     * @param joinPoint
     * @return
     * @throws Throwable
     */
    @Around("@annotation(com.zjjhy.common.annotation.Log)")
    public Object recordLog(ProceedingJoinPoint joinPoint) throws Throwable {
        //每次请求拦截器都会验证jwt令牌，并把令牌中的用户id，也就是创建jwt时使用的载荷（id）拿出来放到本地线程变量副本中，因此不用担心id是否是全局的唯一的,因为每次请求都会重新获取用户id
        String userId = String.valueOf(BaseContext.getCurrent());

        if (ObjectUtil.isEmpty(userId)) {
            throw new BusinessException(ResultCodeEnum.TOKEN_CHECK_ERROR);
        }
        //记录方法执行前的年月日时分秒
        LocalDateTime operateTime = LocalDateTime.now();

        //获取操作类名
        String className = joinPoint.getTarget().getClass().getName();

        //获取操作方法名
        String methodName = joinPoint.getSignature().getName();

        //操作方法参数
        Object[] args = joinPoint.getArgs();
        String methodParams = Arrays.toString(args);

        //记录方法开始前的时间戳
        Long begin = System.currentTimeMillis();

        //开始执行切入点方法并拿到切入点方法的返回值
        Object resultValue = joinPoint.proceed();

        //记录方法开始后的时间戳
        Long end = System.currentTimeMillis();

        //记录方法返回值
        String returnValue = JSONObject.toJSONString(resultValue);

        //记录方法执行总耗时
        Long costTime = end - begin;

        OperateLog operateLog = new OperateLog(null, userId, operateTime, className, methodName, methodParams, returnValue, costTime);

        try {
            operateLogMapper.insertLog(operateLog);
            log.info("操作日志记录成功，日志信息：{}", operateLog);
        } catch (MyBatisSystemException e) {
            throw new BusinessException(ResultCodeEnum.LOG_ERROR);
        }

        //必须return，这里的返回值是用的原方法的返回值，不return的话就给前端响应了个空响应体
        return resultValue;
    }
}

```

## 学长祖传代码之文件上传实现案例

```java
package com.zjjhy.controller.common;

import cn.hutool.core.io.FileUtil;
import cn.hutool.core.thread.ThreadUtil;
import cn.hutool.core.util.StrUtil;
import com.zjjhy.pojo.vo.Result;
import jakarta.servlet.http.HttpServletResponse;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.web.bind.annotation.*;
import org.springframework.web.multipart.MultipartFile;

import java.io.IOException;
import java.io.OutputStream;
import java.net.URLEncoder;
import java.nio.charset.StandardCharsets;

/**
 * 文件上传
 */
@RestController
@RequestMapping("/files")
public class FileController {
    //文件上传存储路径
    //System.getProperty("user.dir") //当前用户的工作目录:  D:\develop\idea_project\my_blog
    private static final String filePath = System.getProperty("user.dir") + "/files/"; //D:\develop\idea_project\my_blog/files/

    //注入yml配置文件中的配置
    @Value("${server.port}")
    private String port; //8088

    //注入yml配置文件中的配置
    @Value("${server.host}")
    private String ip; //localhost

    /**
     * 上传文件
     *
     * @param file
     * @return
     */
    @PostMapping("/upload")
    public Result upload(MultipartFile file) {
        //1.文件名后缀
        String flag; //1732069777447

        //2.同步代码块用当前类的字节码文件当做锁对象;避免其他线程操作干扰创建文件名后缀
        synchronized (FileController.class) {
            flag = System.currentTimeMillis() + "";
            //当前线程暂停1ms确保每次文件名后缀不一样
            ThreadUtil.sleep(1L);
        }

        //3.获取原始文件名
        String fileName = file.getOriginalFilename();

        //4.判断filePath是否指向一个目录 不是则创建目录，否结束
        if (!FileUtil.isDirectory(filePath)) {
            FileUtil.mkdir(filePath);
        }

        //5.指定目录下写入文件字节可能会发生异常
        try {
            FileUtil.writeBytes(file.getBytes(), filePath + flag + "-" + fileName); //D:\develop\idea_project\my_blog/files/1732069777447-原始文件名.png
            System.out.println(fileName + "--上传成功");
        } catch (IOException e) {
            System.out.println(fileName + "--文件上传失败");
        }

        //6.拼接访问文件的url路径
        String http = "http://" + ip + ":" + port + "/files/";

        //7.返回文件上传后的url路径
        return Result.success(http + flag + "-" + fileName); //http://localhost:8088/files/1697438073596-img.png
    }

    /**
     * 获取文件 通过servlet响应对象响应数据
     * HttpServletResponse 对象，可以直接设置响应头、响应状态码和响应体内容。
     * 这种方式适用于需要更细粒度控制响应的情况，例如文件下载、流式传输等。
     *
     * @param flag
     * @param response
     */
    @GetMapping("/{flag}") //1732069777447-原始文件名.png
    public void imgPath(@PathVariable String flag, HttpServletResponse response) {
        //定义一个输出流对象，用于后续文件下载操作
        OutputStream os;

        try {
            //是不是空
            if (StrUtil.isNotEmpty(flag)) {
                //设置HTTP响应头，指示浏览器以附件形式下载文件，并处理文件名的UTF-8编码，防止中文乱码问题
                response.addHeader("Content-Disposition", "attachment;filename=" + URLEncoder.encode(flag, StandardCharsets.UTF_8));
                //设置HTTP响应的内容类型为二进制，适合文件下载
                response.setContentType("application/octet-stream");
                //阅读指定路径下的文件的字节到字节数组中
                byte[] bytes = FileUtil.readBytes(filePath + flag);
                //获取HTTP响应的输出流对象，用于向响应中写入数据
                os = response.getOutputStream();
                //将文件内容写入HTTP响应中，实现文件下载
                os.write(bytes);
                //刷新输出流，确保所有数据被写入
                os.flush();
                //关闭输出流，释放系统资源
                os.close();
            }
        } catch (IOException e) {
            //捕获处理
            System.out.println("文件下载失败");
        }
    }

    @DeleteMapping("/{flag}")
    public void delFile(@PathVariable String flag) {
        FileUtil.del(filePath + flag);
        System.out.println("删除文件" + flag + "成功");
    }
}
```

## SpringBoot的yml配置文件实现多环境切换

### 1.通过设置 spring.profiles.active 属性，Spring Boot 会自动加载对应的环境配置文件。这样，你可以在不同的环境中使用不同的配置，而不需要修改代码。

1.1 主配置yaml文件（application.yml）

```yaml
server:
  port: ${blog.local.datasource.port}
  host: ${blog.local.datasource.host}

spring:
  # 激活配置文件
  profiles:
    # 不同环境下的配置文件名 例：application-develop.yml
    # 根据"-"后面的名字来决定加载哪个环境配置文件
    active: develop
  # 允许Spring Boot 或其他 YAML 解析器会允许对象之间的循环引用关系(我引用你的属性，你引用我的属性)
  main:
    allow-circular-references: true
  datasource:
    driver-class-name: ${blog.datasource.driver-class-name}
    url: jdbc:mysql://${blog.datasource.host}:${blog.datasource.port}/${blog.datasource.database}?useUnicode=true&characterEncoding=utf-8&allowMultiQueries=true&useSSL=false&serverTimezone=GMT%2b8&allowPublicKeyRetrieval=true
    username: ${blog.datasource.username}
    password: ${blog.datasource.password}
  data:
    redis:
      host: ${blog.redis.host}
      port: ${blog.redis.port}
      password: ${blog.redis.password}}
      database: ${blog.redis.database}
  servlet:
    multipart:
      #单个文件大小
      max-file-size: 100MB
      #总文件上传的大小
      max-request-size: 100MB

mybatis:
  mapper-locations: classpath:mapper/*.xml
  type-aliases-package: com.zjjhy.entity
  configuration:
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
    map-underscore-to-camel-case: true
```

1.2 开发环境配置文件（application-develop.yml）

```yaml
blog:
  #数据库数据源
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    host: localhost
    port: 3306
    database: blog
    username: root
    password: root
  #本地主机ip和端口
  local:
    datasource:
      host: localhost
      port: 8088
  #redis数据源
  redis:
    host: localhost
    port: 6379
    password: 123456
    database: 10
```

## 总结

Spring Boot 通过自动配置和起步依赖大大简化了 Spring 应用的开发过程。它提供了一种快速、高效的方式来构建独立的、生产级别的应用程序。

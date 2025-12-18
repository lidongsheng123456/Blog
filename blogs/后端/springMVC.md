---
title: SpringMVC
date: 2024/08/26
tags:
  - Spring全家桶
categories:
  - 后端
---

# Spring MVC 简介

Spring MVC 是 Spring 框架的一部分，用于构建 Web 应用程序。它遵循 Model-View-Controller (MVC)
设计模式，将应用程序的逻辑层、数据层和展示层分离，使得代码更加模块化和易于维护。

## 基本概念

- **Model (模型)**：负责数据的存储和处理，通常包括业务逻辑和数据访问层。
- **View (视图)**：负责显示数据，通常是用户界面。
- **Controller (控制器)**：负责处理用户请求，调用模型和视图来完成用户的请求。

## SpringMVC 的核心组件

1. **DispatcherServlet**：前端控制器，接收所有 HTTP 请求，并将它们分发到相应的处理器。
2. **HandlerMapping**：处理器映射，负责将请求映射到相应的处理器（Controller）。
3. **Controller**：处理器，处理具体的业务逻辑。
4. **ViewResolver**：视图解析器，负责将逻辑视图名称解析为具体的视图实现。
5. **ModelAndView**：模型和视图的封装对象，包含模型数据和视图信息。

## SpringMVC常用注解

* **类级别注解**
    * @Controller：标记一个类作为控制器，处理 HTTP 请求。
    * @RestController：组合了 @Controller 和 @ResponseBody，用于创建 RESTful 风格的控制器。
    * @RequestMapping：用于映射 HTTP 请求到控制器的方法上。可以作用于类或方法级别。
    * @GetMapping：用于映射 HTTP GET 请求到控制器的方法上。
    * @PostMapping：用于映射 HTTP POST 请求到控制器的方法上。
    * @PutMapping：用于映射 HTTP PUT 请求到控制器的方法上。
    * @DeleteMapping：用于映射 HTTP DELETE 请求到控制器的方法上。
    * @PatchMapping：用于映射 HTTP PATCH 请求到控制器的方法上。
* **方法级别注解**
    * @RequestBody：用于将 HTTP 请求体中的数据绑定到方法参数上。
    * @ResponseBody：用于将控制器方法的返回值直接写入 HTTP 响应体中。
    * @PathVariable：用于从 URL 中提取路径变量并绑定到方法参数上。
    * @RequestParam：用于从请求参数中提取数据并绑定到方法参数上。
    * @RequestHeader：用于从请求头中提取数据并绑定到方法参数上。
    * @CookieValue：用于从 Cookie 中提取数据并绑定到方法参数上。
    * @ModelAttribute：用于将请求参数绑定到模型属性上，或者在处理方法调用前预填充模型属性。
* **异常处理注解**
    * @ExceptionHandler：用于处理特定类型的异常，并返回自定义的响应。
    * @ControllerAdvice：用于定义全局的异常处理类，可以应用于多个控制器。
* **数据校验注解**
    * @Valid：用于触发 JSR-303 校验框架的校验。
    * @Validated：用于触发 Spring 的校验机制。
    * @NotNull、@NotEmpty、@Size 等：用于指定字段的校验规则。
* **其他注解**
    * @SessionAttributes：用于将模型属性存储在 HTTP 会话中。
    * @MatrixVariable：用于从 URL 路径中的矩阵变量中提取数据。
    * @CrossOrigin：用于启用跨域请求支持。

## SpringMvcConfig 配置

```java
package com.example.config;

import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.EnableWebMvc;

@Configuration
@ComponentScan({"com.example.controller", "com.example.config"})
@EnableWebMvc
public class SpringMvcConfig {

}
```

## 拦截器

### 拦截器概念

* **拦截器：** 是一种动态拦截方法调用的机制
* **作用：** 1.在指定的方法调用前后执行预先设定的代码；2.阻止原始方法的执行

### 拦截器与过滤器区别

* **归属不同：** Filter属于Servlet技术，Interceptor属于SpringMVC技术
* **拦截内容不同：** Filter对所有访问进行增强，Interceptor仅针对SpringMVC的访问进行增强

### 拦截器入门案例

1. 声明拦截器的bean，并实现HandlerInterceptor接口（注意：扫描加载bean）

```java

@Component
public class ProjectInterceptor implements HandlerInterceptor {
    @Override
    /**
     * 实用最强使用最多的
     */
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        System.out.println("preHandle...");
        return true;
    }

    @Override
    /**
     * ModelAndView 页面跳转
     */
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        System.out.println("postHandle...");
    }

    @Override
    /**
     * Exception 拿到程序执行过程中抛出的异常
     */
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        System.out.println("afterCompletion...");
    }
}
```

2. 定义配置类，继承WebMvcConfigurationSupport，实现addInterceptor方法(注意:扫描加载配置)

```java

@Configuration
public class SpringMvcSupport extends WebMvcconfigurationsupport {
    @Overridepublic
    public void addInterceptors(InterceptorRegistry registry) {
        ...
    }
}
```

3. 添加拦截器并设定拦截的访问路径，路径可以通过可变参数设置多个

```java

@Configuration
public class SpringMvcSupport extends WebMvcConfigurationsupport {
    @Autowired

    private ProjectInterceptor projectInterceptor;

    @override
    public void addInterceptors(InterceptorRegistry registry) {
        //                                          访问所有路径进行拦截       除了login
        registry.addInterceptor(projectInterceptor).addPathPatterns("/**").excludePathPatterns("/login");
    }
}
```

4. 使用标准接口WebMvcConfigurer简化开发（注意：侵入式较强）

注：这里的“侵入式较强”指的是使用 WebMvcConfigurer 接口进行配置时，会直接修改或扩展 Spring MVC 的默认行为，这种做法可能会导致以下几点影响：

* 依赖增加：应用会更加依赖于 Spring 框架的具体实现细节。
* 灵活性降低：一旦采用这种方式配置，后续如果需要更换框架或调整配置方式，可能会比较麻烦。
* 潜在冲突：如果多个配置类都实现了 WebMvcConfigurer 并进行了相似的配置，可能会产生配置上的冲突。
  因此，在选择是否使用 WebMvcConfigurer 时，需要权衡其带来的便利性和可能引入的问题

```java

@Configuration
@ComponentScan({"com.example.controller"})
@EnableWebMvc
public class SpringMvcConfig implements WebMvcConfigurer {
    @Autowired
    private ProjectInterceptor projectInterceptor;

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(projectInterceptor).addPathPatterns("/books", "/books/*");
    }
}
```

### 拦截器参数

* 前置处理

```java
public boolean preHandle(HttpServletRequest request,
                         HttpServletResponse response,
                         Object handler) throws Exception {
    System.out.println("preHandle...");
    return true;
}
```

* **参数**
    * request:请求对象
    * response:响应对象
    * handler:被调用的处理器对象，本质上是一个方法对象，对反射技术中的Method对象进行了再包装

* **返回值**
    * 返回值为false，被拦截的处理器将不执行

* 后置处理

```java
public void postHandle(HttpServletRequest request,
                       HttpServletResponse response,
                       Object handler,
                       ModelAndView modelAndView) throws Exception {
    System.out.println("postHandle...");
}
```

* 参数
    * modelAndView:如果处理器执行完成具有返回结果，可以读取到对应数据与页面信息，并进行调整

* 完成后处理

```java
public void afterCompletion(HttpServletRequest request,
                            HttpServletResponse response,
                            Object handler,
                            Exception ex) throws Exception {
    System.out.println("afterCompletion...");
}
```

* 参数
    * ex:如果处理器执行过程中出现异常对象，可以针对异常情况进行单独处理

### 拦截器链配置以及运行方式

1. 当配置多个拦截器时，形成拦截器链（配置如下）

```java
package com.example.config;

import com.example.controller.interceptor.ProjectInterceptor;
import com.example.controller.interceptor.ProjectInterceptor2;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.InterceptorRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurationSupport;

@Configuration
public class SpringMvcSupport extends WebMvcConfigurationSupport {

    @Autowired
    private ProjectInterceptor projectInterceptor;
    @Autowired
    private ProjectInterceptor2 projectInterceptor2;

    @Override
    /**
     * 先进后出 第一次进入时先执行方法里面第一个的拦截器 第二次则是第二个
     */
    protected void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(projectInterceptor).addPathPatterns("/**");
        registry.addInterceptor(projectInterceptor2).addPathPatterns("/**");
    }
}
```

2. 拦截器的运行顺序

* preHandle:与配置顺序相同，必定运行
* postHandle:与配置顺序相反，可能不运行
* afterCompletion:与配置顺序相反，可能不运行

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

### 总结

* **继承 Exception：**
    * 检查型异常：编译器要求必须处理这些异常。如果方法中抛出了检查型异常，要么在方法签名中声明 throws，要么在方法内部使用
      try-catch 块捕获并处理。
    * 适用场景：适用于可以预见并且应该由调用者处理的异常情况，如 IOException、SQLException 等。
* **继承 RuntimeException：**
    * 非检查型异常：编译器不要求必须处理这些异常。即使方法中抛出了非检查型异常，也不需要在方法签名中声明
      throws，也不强制要求在方法内部捕获和处理。
    * 适用场景：适用于编程错误或不应该发生的情况，如 NullPointerException、ArrayIndexOutOfBoundsException 等。

* 继承 Exception：检查型异常，编译器强制处理。
* 继承 RuntimeException：非检查型异常，编译器不要求处理。

## 案例

### 先准备好配置类（SpringMvcConfig.java和ServletConfig.java）

```java
SpringMvcConfig.java

        package com.example.config;

import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.EnableWebMvc;

@Configuration
@EnableWebMvc
//1.扫描表现层应用于springMVC应用上下文；       2.扫描拦截器配置类
@ComponentScan({"com.example.controller", "com.example.config"})
public class SpringMvcConfig {
}
```

```java
ServletConfig.java

        package com.example.config;

import org.springframework.web.filter.CharacterEncodingFilter;
import org.springframework.web.servlet.support.AbstractAnnotationConfigDispatcherServletInitializer;

import javax.servlet.Filter;

/**
 * 集成SSM（Spring + Spring MVC + MyBatis）的配置到Servlet容器中。
 * 将此配置类打成WAR包并部署到Tomcat中，可以实现请求响应的功能。
 * <p>
 * 该类继承自AbstractAnnotationConfigDispatcherServletInitializer，用于配置Spring MVC和Spring核心配置。
 * 通过重写几个关键方法，可以指定Servlet的映射路径、Spring MVC配置类、根上下文配置类以及过滤器。
 */
public class ServletConfig extends AbstractAnnotationConfigDispatcherServletInitializer {
    /**
     * 配置Servlet的映射路径
     * <p>
     * 此方法返回Servlet的URL映射路径数组在这个示例中，它将Servlet映射到根路径（"/"），
     * 这意味着该Servlet将处理所有进入应用的请求
     *
     * @return String[] 包含Servlet映射路径的数组
     */
    @Override
    protected String[] getServletMappings() {
        return new String[]{"/"};
    }

    /**
     * 配置Servlet的Spring配置类
     * <p>
     * 此方法返回一个包含Spring MVC配置类的数组这个配置类包含了Spring MVC的特定配置，
     * 如请求处理器映射、视图解析器等
     *
     * @return Class<?>[] 包含Spring MVC配置类的数组
     */
    @Override
    protected Class<?>[] getServletConfigClasses() {
        return new Class[]{SpringMvcConfig.class};
    }

    /**
     * 配置根上下文的Spring配置类
     * <p>
     * 此方法返回一个包含根上下文配置类的数组这个配置类通常包含服务层组件、数据源配置等，
     * 是应用的核心配置
     *
     * @return Class<?>[] 包含根上下文配置类的数组
     */
    @Override
    protected Class<?>[] getRootConfigClasses() {
        return new Class[]{SpringConfig.class};
    }

    /**
     * 配置Servlet的过滤器
     * <p>
     * 此方法返回一个包含过滤器的数组这些过滤器将在Servlet处理请求前对请求进行处理在这个示例中，
     * 它创建了一个字符编码过滤器，用于设置请求和响应的字符编码为UTF-8
     *
     * @return Filter[] 包含过滤器的数组
     */
    @Override
    protected Filter[] getServletFilters() {
        CharacterEncodingFilter filter = new CharacterEncodingFilter();
        filter.setEncoding("UTF-8");
        return new Filter[]{filter};
    }
}

```

### 1.controller（表现层）

```java
package com.example.controller;

import com.example.pojo.Book;
import com.example.service.BookService;
import com.example.vo.Code;
import com.example.vo.Result;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;

import java.util.List;

@RestController
@RequestMapping("/books")
public class BookController {
    @Autowired
    private BookService bookService;

    @PostMapping
    public Result save(@RequestBody Book book) {
        boolean flag = bookService.save(book);
        return new Result(flag ? Code.SAVE_OK : Code.SAVE_ERR, flag);
    }

    @PutMapping
    public Result update(@RequestBody Book book) {
        boolean flag = bookService.update(book);
        return new Result(flag ? Code.UPDATE_OK : Code.UPDATE_ERR, flag);
    }

    @DeleteMapping("/{id}")
    public Result delete(@PathVariable Integer id) {
        boolean flag = bookService.delete(id);
        return new Result(flag ? Code.DELETE_OK : Code.DELETE_ERR, flag);
    }

    @GetMapping("/{id}")
    public Result getById(@PathVariable Integer id) {
        Book book = bookService.getById(id);
        Integer code = book != null ? Code.GET_OK : Code.GET_ERR;
        String msg = book != null ? "success" : "数据查询失败，请重试";
        return new Result(code, msg, book);
    }

    @GetMapping
    public Result getAll() {
        List<Book> bookList = bookService.getAll();
        Integer code = bookList != null ? Code.GET_OK : Code.GET_ERR;
        String msg = bookList != null ? "success" : "数据查询失败，请重试";
        return new Result(code, msg, bookList);
    }
}
```

### 2.Service接口（业务层）

```java
package com.example.service;

import com.example.pojo.Book;
import org.springframework.transaction.annotation.Transactional;

import java.util.List;

/**
 * 使用Spring框架的事务管理功能
 * 标记该方法为一个事务性操作
 * 当方法执行成功时，事务提交
 * 当方法执行失败时，事务回滚
 */
@Transactional

public interface BookService {

    /**
     * 保存
     *
     * @param book
     * @return
     */
    public boolean save(Book book);

    /**
     * 修改
     *
     * @param book
     * @return
     */
    public boolean update(Book book);

    /**
     * 根据id删除
     *
     * @param id
     * @return
     */
    public boolean delete(Integer id);

    /**
     * 按id查询
     *
     * @param id
     * @return
     */
    public Book getById(Integer id);

    /**
     * 查询全部
     *
     * @return
     */
    public List<Book> getAll();
}

```

### 2.1ServiceImpl（业务接口实现类）

```java
package com.example.service.impl;

import com.example.exception.BusinessException;
import com.example.exception.SystemException;
import com.example.mapper.BookMapper;
import com.example.pojo.Book;
import com.example.service.BookService;
import com.example.vo.Code;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.List;

@Service
public class BookServiceImpl implements BookService {
    @Autowired
    private BookMapper bookMapper;

    @Override
    public boolean save(Book book) {
        bookMapper.save(book);
        return true;
    }

    @Override
    public boolean update(Book book) {
        bookMapper.update(book);
        return true;
    }

    @Override
    public boolean delete(Integer id) {
        bookMapper.delete(id);
        return true;
    }

    @Override
    public Book getById(Integer id) {
        if (id == 1) {
            throw new BusinessException("错误", Code.BUSINESS_ERR);
        }
        try {
            int i = 1 / 0;
        } catch (ArithmeticException e) {
            throw new SystemException("服务器异常", e, Code.SYSTEM_TIMEOUT_ERR);
        }

        return bookMapper.getById(id);
    }

    @Override
    public List<Book> getAll() {
        return bookMapper.getAll();
    }
}

```

### 3.Mapper（数据访问层）

```java
package com.example.mapper;

import com.example.pojo.Book;
import org.apache.ibatis.annotations.Delete;
import org.apache.ibatis.annotations.Insert;
import org.apache.ibatis.annotations.Select;
import org.apache.ibatis.annotations.Update;
import org.springframework.stereotype.Repository;

import java.util.List;

@Repository
public interface BookMapper {
    @Insert("INSERT into tbl_book (type, name, description) values (#{type}, #{name}, #{description})")
    public void save(Book book);

    @Update("update tbl_book set type = #{type}, name = #{name}, description = #{description} where id = #{id}")
    public void update(Book book);

    @Delete("DELETE from tbl_book where id = #{id}")
    public void delete(Integer id);

    @Select("select * from tbl_book where id = #{id}")
    public Book getById(Integer id);

    @Select("select * from tbl_book")
    public List<Book> getAll();
}

```

### 3.1Mapper（映射文件）

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.example.mapper.BookMapper">
    <select id="方法名" resultType="com.example.pojo.Book">

    </select>
</mapper>

```

## 总结

Spring MVC 是一个强大的框架，用于构建灵活和可扩展的 Web 应用程序。通过合理的配置和组件设计，可以有效地分离关注点，提高开发效率和代码质量。

---
title: knife4j
date: 2025/2/25
tags:
  - knife4j
categories:
  - 后端
---

## 在springboot3.0.6中整合Knife4j

1. 添加依赖： 在pom.xml文件中添加Knife4j的依赖。Knife4j是Swagger的增强UI，因此也需要Swagger的依赖。

```xml

<dependency>
    <groupId>com.github.xiaoymin</groupId>
    <artifactId>knife4j-openapi3-jakarta-spring-boot-starter</artifactId>
    <version>4.3.0</version>
</dependency>
```

2. 配置文件

```yaml
# 配置Springdoc的属性，用于生成和管理Swagger API文档
springdoc:
  # Swagger UI页面的配置
  swagger-ui:
    # Swagger UI页面的访问路径
    path: /swagger-ui.html
    # 标签的排序方式，这里选择字母排序
    tags-sorter: alpha
    # 操作的排序方式，这里选择字母排序
    operations-sorter: alpha
  # API文档的路径配置
  api-docs:
    # API文档的访问路径
    path: /v3/api-docs
    enabled: true
  # API文档的组配置
  group-configs:
    - group: '用户端'   # API分组名称
      paths-to-match: '/**'  # 匹配所有路径
      packages-to-scan: com.example.controller.user  # 扫描的包，用于自动发现API

    - group: '管理端'   # API分组名称
      paths-to-match: '/**'  # 匹配所有路径
      packages-to-scan: com.example.controller.admin  # 扫描的包，用于自动发现API

    - group: '通用接口'   # API分组名称
      paths-to-match: '/**'  # 匹配所有路径
      packages-to-scan: com.example.controller.common  # 扫描的包，用于自动发现API

# knife4j的增强配置，不需要增强可以不配
knife4j:
  enable: true    #开启knife4j,无需添加@EnableKnife4j注解
  setting:
    language: zh_cn   #中文
    swagger-model-name: 实体列表   #默认为：Swagger Models
  #开启Swagger的Basic认证功能,默认是false,开启后访问文档页面时会弹出用户名和密码输入框
  basic:
    enable: true
    # Basic认证用户名
    username: user
    # Basic认证密码
    password: 123456

```

3. 配置类WebMvcConfiguration配置上Swagger

```java
package com.example.framework.config;

import cn.dev33.satoken.filter.SaServletFilter;
import cn.dev33.satoken.interceptor.SaInterceptor;
import cn.dev33.satoken.stp.StpUtil;
import io.swagger.v3.oas.models.OpenAPI;
import io.swagger.v3.oas.models.info.Info;
import io.swagger.v3.oas.models.info.License;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.InterceptorRegistry;
import org.springframework.web.servlet.config.annotation.ResourceHandlerRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

@Configuration
public class WebMvcConfiguration implements WebMvcConfigurer {

    @Bean
    public OpenAPI springShopOpenAPI() {
        return new OpenAPI()
                .info(new Info()
                        .title("东神后台接口文档")
                        .description("东神后台API文档")
                        .version("v1")
                        .license(new License().name("Apache 2.0").url("https://springdoc.org"))
                        .contact(new io.swagger.v3.oas.models.info.Contact()
                                .name("李东升")
                                .url("https://www.lidongshen.top/")
                                .email("208550738@qq.com")));
    }

    /**
     * 设置静态资源映射
     */
    @Override
    public void addResourceHandlers(ResourceHandlerRegistry registry) {
        // 添加静态资源映射规则
        registry.addResourceHandler("/static/**").addResourceLocations("classpath:/static/");
        //配置 knife4j 的静态资源请求映射地址
        registry.addResourceHandler("/doc.html")
                .addResourceLocations("classpath:/META-INF/resources/");
        registry.addResourceHandler("/webjars/**")
                .addResourceLocations("classpath:/META-INF/resources/webjars/");
    }
}
```

4. 常用注解

* 类级别注解
    * `@Tag`:用于为API操作分组。
      ```java
      @Tag(name = "用户管理", description = "用户相关操作")
      @RestController
      @RequestMapping("/users")
      public class UserController {
      // ...
      }
      ```

* 方法级别注解
    * `@Operation`:用于描述控制器类中的单个操作。
      ```java
      @Operation(summary = "获取用户列表", description = "返回所有用户的列表")
      @GetMapping("/users")
      public List<User> getUsers() {
        // ...
      }
      ```

    * `@ApiResponses`:用于描述API操作的响应。
      ```java
      @Operation(summary = "获取用户列表")
      @ApiResponses(value = {
      @ApiResponse(responseCode = "200", description = "成功", content = @Content(mediaType = "application/json", schema = @Schema(implementation = User.class))),
      @ApiResponse(responseCode = "400", description = "请求参数错误"),
      @ApiResponse(responseCode = "404", description = "找不到资源"),
      @ApiResponse(responseCode = "500", description = "服务器内部错误")
      })
      @GetMapping("/users")
      public List<User> getUsers() {
      // ...
      }
      ```

    * `@ApiResponse`:用于描述响应单个参数。
      ```java
      @Operation(summary = "获取用户详情")
      @GetMapping("/{id}")
      public User getUser(@Parameter(description = "用户ID", required = true) @PathVariable Long id) {
       // ...
      }
      ```

* 参数级别注解
    * `@Parameter`:用于描述单个参数。
      ```java
      @Operation(summary = "获取用户详情")
      @GetMapping("/{id}")
      public User getUser(@Parameter(description = "用户ID", required = true) @PathVariable Long id) {
       // ...
      }
      ```

    * `@Parameters`:用于描述多个参数。
      ```java
      @Operation(summary = "分页获取用户列表")
      @Parameters({
      @Parameter(name = "page", description = "页码", required = true, schema = @Schema(type = "integer", example = "1")),
      @Parameter(name = "size", description = "每页数量", required = true, schema = @Schema(type = "integer", example = "10"))
      })
      @GetMapping("/users")
      public List<User> getUsersByPage(@RequestParam int page, @RequestParam int size) {
      // ...
      }
      ```

* 模型类注解
    * `@Schema`:用于描述模型类和字段的信息。
      ```java
      @Schema(description = "用户实体")
      public class User {
          @Schema(description = "用户ID", example = "1")
          private Long id;
    
          @Schema(description = "用户名", example = "Alice")
          private String name;
    
          @Schema(description = "用户年龄", example = "30")
          private Integer age;
      }
      ```

    * `@ArraySchema`:用于描述数组类型的字段。
      ```java
      @Schema(description = "用户列表")
      public class UserListResponse {
        @ArraySchema(schema = @Schema(implementation = User.class), description = "用户数组")
        private List<User> users;
      }
      ```

* 文件上传相关注解

```java

@Operation(summary = "上传文件")
@PostMapping(value = "/upload", consumes = MediaType.MULTIPART_FORM_DATA_VALUE)
public String uploadFile(@Parameter(description = "文件", required = true) @RequestParam("file") MultipartFile file) {
    // ...
}
```

5. 访问Knife4j UI

* 启动Spring Boot应用后，可以通过浏览器访问http://localhost:端口号/doc.html来查看Knife4j的增强UI。

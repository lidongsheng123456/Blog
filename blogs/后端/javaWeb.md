---
title: JavaWeb
date: 2024/04/25
tags:
  - javaEE
categories:
  - 后端
---

# Java Web 简介

Java Web 是指使用 Java 技术开发的 Web 应用程序。它包括了多个技术和框架，如 Servlet、JSP、Spring、Hibernate 等，用于构建动态的
Web 应用。

## 主要组件

### Servlet

Servlet 是运行在服务器上的 Java 类，用于处理客户端请求并生成响应。Servlet 是 Java Web 应用的基础。

### JSP (JavaServer Pages)

JSP 是一种动态网页技术，允许在 HTML 页面中嵌入 Java 代码。JSP 页面最终会被转换成 Servlet 来执行。

### Filter (过滤器)

过滤器用于在请求到达 Servlet 之前或响应返回客户端之前对请求和响应进行预处理或后处理。

### Listener (监听器)

监听器用于监听 Web 应用中的特定事件，如上下文初始化、会话创建等，并在这些事件发生时执行相应的操作。

## 开发环境

### 开发工具

- **IDE**：IntelliJ IDEA、Eclipse、NetBeans 等。
- **服务器**：Tomcat、Jetty、JBoss 等。

### 依赖管理

- **Maven**：用于管理项目依赖和构建。
- **Gradle**：另一种流行的构建工具，支持更灵活的配置。

## 示例项目

### 项目结构

```html
├── src/
│ ├── main/
│ │ ├── java/
│ │ │ └── com/example/
│ │ │ └── MyServlet.java
│ │ └── webapp/
│ │ ├── WEB-INF/
│ │ │ └── web.xml
│ │ └── index.jsp
└── pom.xml

```

### web.xml 配置

```xml

<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd"
         version="3.1">
    <servlet>
        <servlet-name>myServlet</servlet-name>
        <servlet-class>com.example.MyServlet</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>myServlet</servlet-name>
        <url-pattern>/hello</url-pattern>
    </servlet-mapping>
</web-app>
```

### MyServlet.java

```java
package com.example;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.io.PrintWriter;

public class MyServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        response.setContentType("text/html");
        PrintWriter out = response.getWriter();
        out.println("<h1>Hello, World!</h1>");
    }
}
```

### index.jsp

```html
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head><title>Java Web Example</title></head>
<body><h1>Welcome to Java Web!</h1> <a href="hello">Click here to call the servlet</a></body>
</html>
```

## 常用框架

### Spring

Spring 是一个全面的 Java 应用框架，提供了依赖注入、事务管理、安全等功能。Spring MVC 是一个用于构建 Web 应用的模块。

### Hibernate

Hibernate 是一个对象关系映射（ORM）框架，用于将 Java 对象映射到数据库表，并提供持久化管理。

### MyBatis

MyBatis 是一个轻量级的 ORM 框架，提供了 SQL 映射和动态 SQL 功能。

## 总结

Java Web 开发涉及多种技术和框架，通过合理选择和组合这些技术，可以构建高效、可维护的 Web 应用。

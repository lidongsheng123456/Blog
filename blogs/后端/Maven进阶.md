---
title: Maven进阶
date: 2024/11/15
tags:
  - Maven
categories:
  - 后端
---

# Maven进阶

## 学习目标

1. 理解分模块开发的意义
2. 能够使用聚合工程快速构建项目
3. 能够使用继承简化项目配置
4. 能够根据需求配置生产、开发、测试环境，并在各环境间切换运行

## 分模块开发与设计

### 分模块开发意义

* 将原始模块按照功能拆分成若干个模块，方便模块间的相互调用，接口共享

![img.png](img/maven_model_img.png)

**注意事项：** 分模块开发需要先针对模块功能进行设计，在进行编码。不会现将工程开发完毕，然后进行拆分。

## 依赖管理

### 依赖传递

* **依赖具有传递性**
    * 直接依赖：在当前项目中通过依赖配置建立的依赖关系
    * 间接依赖：被资源的资源如果依赖其他资源，当前项目间接依赖其他项目
* **依赖传递冲突问题**
    * 路径优先:当依赖中出现相同的资源时，层级越深，优先级越低，层级越浅，优先级越高
    * 声明优先:当资源在相同层级被依赖时，配置顺序靠前的覆盖配置顺序靠后的
    * 特殊优先:当同级配置了相同资源的不同版本，后配置的覆盖了先配置的

### 可选依赖(主动行为)

* 可选依赖指对外隐藏当前所依赖的资源--不透明

```xml

<dependency>
    <groupId>com.itheima</groupId>
    <artifactId>maven 03_pojo</artifactId>
    <version>1.0-SNAPSHOT</version>
    <!--可选依赖是隐藏当前工程所依赖的资源，隐藏后对应资源将不具有依赖传递性-->
    <optional>false</optional>
</dependency>
```

### 排除依赖(被动行为)

* 排除依赖指主动断开依赖的资源，被排除的资源无需指定版本--不需要

```xml

<dependency>
    <groupId>com.itheima</groupId>
    <artifactId>maven 04 dao</artifactId>
    <version>1.0-SNAPSHOT</version>
    <!--排除依赖是隐藏当前资源对应的依赖关系-->
    <exclusions>
        <exclusion>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
        </exclusion>
        <exclusion>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```

* 排除依赖资源仅指定GA即可，无需指定V

## 聚合管理与继承

### 聚合

* 聚合:将多个模块组织成一个整体，同时进行项目构建的过程称为聚合
* 聚合工程:通常是一个不具有业务功能的“空”工程(有且仅有一个pom文件)
* 作用:使用聚合工程可以将多个工程编组，通过对聚合工程进行构建，实现对所包含的模块进行同步构建
    * 当工程中某个模块发生更新(变更)时，必须保障工程中与已更新模块关联的模块同步更新，此时可以使用聚合工程来解决批量模块同步构建的问题
      本地仓库

1. 创建Maven模块，设置打包类型为pom

```xml

<packaging>pom</packaging>
```

* **注意事项：** 每个maven工程都有对应的打包方式，默认为jar，web工程打包方式为war

2. 设置当前聚合工程所包含的子模块名称

```xml

<modules>
    <module>../maven_ssm</module>
    <module>../maven_pojo</module>
    <module>../maven_dao</module>
</modules>
```

* **注意事项：** 聚合工程中所包含的模块在进行构建时会根据模块间的依赖关系设置构建顺序，与聚合工程中模块的配置书写位置无关参与聚合的工程无法向上感知是否参与聚合，只能向下配置哪些模块参与本工程的聚合

### 继承

* 概念:继承描述的是两个工程间的关系，与java中的继承相似，子工程可以继承父工程中的配置信息，常见于依赖 关系的继承
* 作用:
    * 简化配置
    * 减少版本冲突

1. 创建Maven模块，设置打包类型为pom

```xml

<packaging>pom</packaging>
```

* **注意事项：** 建议父工程打包方式设置为pom

2. 在父工程的pom文件中配置依赖关系(子工程将沿用父工程中的依赖关系)

```xml

<dependencies>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-webmvc</artifactId>
        <version>5.2.10.RELEASE</version>
    </dependency>
</dependencies>
```

3. 配置子工程中可选的依赖关系

```xml

<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid</artifactId>
            <version>1.1.16</version>
        </dependency>
    </dependencies>
</dependencyManagement>
```

4. 在子工程中配置当前工程所继承的父工程

```xml
<!--定义该工程的父工程-->
<parent>
    <groupId>com.itheima</groupId>
    <artifactId>maven_parent</artifactId>
    <version>1.0-SNAPSHOT</version>
    <!--填写父工程的pom文件-->
    <relativePath>../maven parent/pom.xml</relativePath>
</parent>
```

5. 在子工程中配置使用父工程中可选依赖的坐标

```xml

<dependencies>
    <dependency>
        <groupId>com.alibaba</groupId>
        <artifactId>druid</artifactId>
    </dependency>
</dependencies>
```

* **注意事项：** 子工程中使用父工程中的可选依赖时，仅需要提供群组id和项目id，无需提供版本，版本由父工程统一提供，避免版本冲突；子工程中还可以定义父工程中没有定义的依赖关系

### 聚合与继承的区别

* 作用
    * 聚合用于快速构建项目
    * 继承用于快速配置
* 相同点:
    * 聚合与继承的pom.xml文件打包方式均为pom，可以将两种关系制作到同一个pom文件中
    * 聚合与继承均属于设计型模块，并无实际的模块内容
* 不同点:
    * 聚合是在当前模块中配置关系，聚合可以感知到参与聚合的模块有哪些
    * 继承是在子模块中配置关系，父模块无法感知哪些子模块继承了自己

## 属性管理

### 属性配置与使用

1. 定义属性

```xml
<!--定义自定义属性-->
<properties>
    <spring.version>5.2.10.RELEASE</spring.version>
    <junit.version>4.12</junit.version>
</properties>
```

2. 引用属性

```xml

<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>${spring.version}</version>
</dependency>
```

### 资源文件引用属性

1. 定义属性

```xml
<!--定义自定义属性-->
<properties>
    <spring.version>5.2.10.RELEASE</spring.version>
    <junit.version>4.12</junit.version>
    <jdbc.url>jdbc:mysql://127.0.0.1:3306/ssm db</jdbc.url>
</properties>
```

2. 配置文件中引用属性

```properties
jdbc.driver=com.mysql.jdbc.Driver
jdbc.url=${jdbc.url}
jdbc.username=root
jdbc.password=root
```

3. 开启资源文件目录加载属性的过滤器

```xml

<build>
    <resources>
        <resource>
            <directory>${project.basedir}/src/main/resources</directory>
            <filtering>true</filtering>
        </resource>
    </resources>
</build>
```

4. 配置maven打war包时，忽略web.xml检查

```xml

<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-war-plugin</artifactId>
    <version>3.2.3</version>
    <configuration>
        <failonMissingWebXml>false</failonMissingWebXml>
    </configuration>
</plugin>
```

### 其他属性（了解）

![img.png](img/maven_qitashuxing.png)

### 版本管理

* 工程版本:
    * SNAPSHOT(快照版本)
        * 项目开发过程中临时输出的版本，称为快照版本
        * 快照版本会随着开发的进展不断更新
    * RELEASE(发布版本)
        * 项目开发到进入阶段里程碑后，向团队外部发布较为稳定的版本，这种版本所对应的构件文件是稳定的，即便进行功能的后续开发，也不会改变当前发布版本内容，这种版本称为发布版本
* 发布版本
    * alpha版
    * beta版
    * 纯数字版

## 多环境与应用

### 多环境开发

1. 定义多环境

```xml
<!--配置多环境-->
<profiles>
    <!--开发环境-->
    <profile>
        <id>env_dep</id>
        <properties>
            <jdbc.url>jdbc:mysql://127.1.1.1:3306/ssm_db</jdbc.url>
        </properties>
    </profile>
    <!--生产环境-->
    <profile>
        <id>env_pro</id>
        <properties>
            <jdbc.url>jdbc:mysql://127.2.2.2:3306/ssm_db</jdbc.url>
        </properties>
    </profile>
    <!--测试环境-->
    <profile>
        <id>env_test</id>
        <properties>
            <jdbc.url>jdbc:mysql://127.3.3.3:3306/ssm_db</jdbc.url>
        </properties>
    </profile>
</profiles>
```

2. 使用多环境(构建过程)

```shell
mvn 指令 -P 环境定义id
```

范例:

```shell
mvn install -p pro_env
```

### 跳过测试

* 应用场景
    * 功能更新中并且没有开发完毕
    * 快速打包
    * .....

跳过测试

```shell
mvn 指令-D skipTests
```

范例:

```shell
mvn install -D skipTests
```

* **注意事项：** 执行的项目构建指令必须包含测试生命周期，否则无效果。例如执行compile生命周期，不经过test生命周期

* 细粒度控制跳过测试

```xml

<plugin>
    <artifactId>maven-surefire-plugin</artifactId>
    <version>2.22.1</version>
    <configuration>
        <skipTests>true</skipTests><!--设置跳过测试-->
        <includes><!--包含指定的测试用例-->
            <include>**/User*Test.java</include>
        </includes>
        <excludes><!--排除指定的测试用例-->
            <exclude>**/User*TestCase.java</exclude>
        </excludes>
    </configuration>
</plugin>
```

## 私服

### 私服简介

* 私服是一台独立的服务器，用于解决团队内部的资源共享与资源同步问题
* Nexus
    * Sonatype公司的一款maven私服产品
    * 下载地址:https://help.sonatype.com/repomanager3/download

#### Nexus安装与启动

* 启动服务器(命令行启动)

  `nexus.exe run nexus`

* 访问服务器(默认端口:8081)

  `http://localhost:8081`

* 修改基础配置信息
    * 安装路径下etc目录中nexus-default.properties文件保存有nexus基础配置信息，例如默认访问端口
* 修改服务器运行配置信息
    * 安装路径下bin目录中nexus.vmoptions文件保存有nexus服务器启动对应的配置信息，例如默认占用内存空间

**休息一会，马上回来**

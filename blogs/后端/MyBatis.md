---
title: MyBatis
date: 2024/05/26
tags:
  - MyBatis
categories:
  - 后端
---

# MyBatis 简介

MyBatis 是一个优秀的持久层框架，它支持定制化 SQL、存储过程以及高级映射。MyBatis 避免了几乎所有的 JDBC
代码和手动设置参数以及获取结果集。MyBatis 可以使用简单的 XML 或注解进行配置和原始映射，将接口和 Java 的 POJOs (Plain Old
Java Objects) 映射成数据库中的记录。

## 主要特性

- **简单易用**：MyBatis 提供了简洁的 API 和配置，使得开发者可以快速上手。
- **灵活性**：支持动态 SQL，可以根据条件生成不同的 SQL 语句。
- **性能**：通过缓存机制提高查询性能。
- **分离 SQL 与代码**：SQL 语句可以放在 XML 文件中，便于管理和维护。

## 快速入门

### 添加依赖

在 `pom.xml` 中添加 MyBatis 的依赖：

```xml

<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis</artifactId>
    <version>3.5.7</version>
</dependency>
```

### 配置文件

#### mybatis-config.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN" "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/mydatabase"/>
                <property name="username" value="root"/>
                <property name="password" value="rootpassword"/>
            </dataSource>
        </environment>
    </environments>
    <mappers>
        <mapper resource="UserMapper.xml"/>
    </mappers>
</configuration>
```

### 创建实体类

```java
public class User {
    private int id;
    private String username;
    private String password;
    private String email;
// Getters and Setters
}
```

### 创建 Mapper 接口

```java
public interface UserMapper {
    List<User> getAllUsers();

    User getUserById(int id);

    void insertUser(User user);

    void updateUser(User user);

    void deleteUser(int id);
}
```

### 创建 Mapper XML 文件

#### UserMapper.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.example.mapper.UserMapper">
    <select id="getAllUsers" resultType="com.example.entity.User">SELECT * FROM users</select>
    <select id="getUserById" parameterType="int" resultType="com.example.entity.User">
        SELECT * FROM users WHERE id = #{id}
    </select>

    <insert id="insertUser" parameterType="com.example.entity.User">
        INSERT INTO users (username, password, email) VALUES (#{username}, #{password}, #{email})
    </insert>

    <update id="updateUser" parameterType="com.example.entity.User">
        UPDATE users SET username = #{username}, password = #{password}, email = #{email} WHERE id = #{id}
    </update>

    <delete id="deleteUser" parameterType="int">
        DELETE FROM users WHERE id = #{id}
    </delete>
</mapper>
```

### 使用 MyBatis

```java
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;

import java.io.IOException;
import java.io.Reader;
import java.util.List;

public class MyBatisExample {
    public static void main(String[] args) {
        String resource = "mybatis-config.xml";
        Reader reader;
        try {
            reader = Resources.getResourceAsReader(resource);
            SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(reader);
            SqlSession session = sqlSessionFactory.openSession();
            UserMapper userMapper = session.getMapper(UserMapper.class);

            // 查询所有用户
            List<User> users = userMapper.getAllUsers();
            for (User user : users) {
                System.out.println(user);
            }

            // 插入新用户
            User newUser = new User();
            newUser.setUsername("john");
            newUser.setPassword("secret");
            newUser.setEmail("john@example.com");
            userMapper.insertUser(newUser);
            session.commit(); // 提交事务

            session.close();// 关闭会话
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

## 常用动态 SQL

MyBatis 支持动态 SQL，可以根据条件生成不同的 SQL 语句。

### 示例

1. **if 标签：** 用于条件判断，当条件满足时，会将标签内的SQL片段加入到最终的SQL语句中。

```xml

<if test="materialId != null">material_id = #{materialId},</if>
```

2. **choose、when 和 otherwise 标签：** 类似于Java中的switch语句，用于多条件选择。

```xml

<choose>
    <when test="materialId != null">material_id = #{materialId}</when>
    <when test="productLineName != null">product_line_name = #{productLineName}</when>
    <otherwise>remark = #{remark}</otherwise>
</choose>

```

3. **where 标签：** 自动处理WHERE关键字，当没有条件时不会生成WHERE关键字，避免SQL语法错误。

```xml

<where>
    <if test="materialId != null">and material_id = #{materialId}</if>
    <if test="productLineName != null">and product_line_name like concat('%', #{productLineName}, '%')</if>
</where>

```

4. **set 标签：** 自动处理SET关键字，当没有条件时不会生成SET关键字，避免SQL语法错误。

```xml

<set>
    <if test="materialId != null">material_id = #{materialId},</if>
    <if test="productLineName != null">product_line_name = #{productLineName},</if>
    <if test="remark != null">remark = #{remark},</if>
</set>

```

5. **foreach 标签：** 用于遍历集合，常用于IN子句。

```xml
<!--
    item: 每个元素的别名，可以在标签内部使用这个别名来引用当前元素。
    collection: 需要遍历的集合名称。可以是以下几种形式：
        列表：list
        数组：array
        Map 中的键：map.key
        对象中的属性：object.property
    open: 在遍历结果前面添加的内容，通常用于添加括号或逗号等。
    separator: 集合元素之间的分隔符，通常用于分隔多个值。
    close: 在遍历结果后面添加的内容，通常用于添加括号或逗号等。
    
    示例:
    假设有一个 UserMapper.xml 文件，其中有一个查询需要根据多个用户 ID 来获取用户信息：
-->
<select id="getUsersByIds" parameterType="java.util.List" resultType="com.example.entity.User">
    SELECT * FROM users WHERE id IN
    <foreach item="id" collection="list" open="(" separator="," close=")">
        #{id}
    </foreach>
</select>
```

6. **trim 标签(等价于set标签)：** 用于添加或去除指定的前缀和后缀，并且可以去除指定的多余字符。

```xml
<!--
    prefix: 在标签内容前面添加的内容。
    suffix: 在标签内容后面添加的内容。
    prefixOverrides: 去除前缀中的指定字符。
    suffixOverrides: 去除后缀中的指定字符。
    
    示例:
    在这个示例中，trim 标签实际上是 set 标签的一个复杂化形式，等价于直接用<set></set>
-->
<update id="updateUser" parameterType="com.example.entity.User">
    UPDATE users
    <trim prefix="SET" suffixOverrides=",">
        <if test="username != null">username = #{username},</if>
        <if test="password != null">password = #{password},</if>
        <if test="email != null">email = #{email},</if>
    </trim>
    WHERE id = #{id}
</update>
```

7. **sql标签：** 用于定义可重用的 SQL 片段。

```xml

<sql id="selectMesFactoryModelVo">
    select id, material_id, product_line_name, remark
    from mes_factory_model
</sql>

```

8. **resultMap标签：** 用于定义结果映射，将查询结果映射到 Java 对象。

```xml

<resultMap type="MesFactoryModel" id="MesFactoryModelResult">
    <result property="id" column="id"/>
    <result property="materialId" column="material_id"/>
    <result property="productLineName" column="product_line_name"/>
    <result property="remark" column="remark"/>
</resultMap>
```

9. **include标签：** 用于引用 <sql></sql> 标签定义的 SQL 片段。

```xml

<select id="selectMesFactoryModelList" parameterType="MesFactoryModel" resultMap="MesFactoryModelResult">
    <include refid="selectMesFactoryModelVo"/>
    <where>
        <if test="materialId != null ">and material_id = #{materialId}</if>
        <if test="productLineName != null  and productLineName != ''">and product_line_name like concat('%',
            #{productLineName}, '%')
        </if>
    </where>
</select>
```

10. **resultMap标签：** 用于配置映射关系

```xml

<resultMap type="Profile" id="ProfileResult">
    <result property="id" column="profile_id"/>
    <result property="bio" column="bio"/>
</resultMap>
```

11. **association标签：** association 标签用于将查询结果中的某个字段映射到一个复杂的 Java 对象，适用于一对一的关系。

```xml
<!-- 定义 Profile 结果映射 -->
<resultMap type="Profile" id="ProfileResult">
    <result property="id" column="profile_id"/>
    <result property="bio" column="bio"/>
</resultMap>

        <!-- 定义 User 结果映射，并关联 Profile -->
<resultMap type="User" id="UserWithProfileResult">
<result property="id" column="user_id"/>
<result property="username" column="username"/>
<association property="profile" javaType="Profile" resultMap="ProfileResult"/>
</resultMap>

        <!-- 查询语句 -->
<select id="getUserWithProfile" parameterType="int" resultMap="UserWithProfileResult">
SELECT u.id AS user_id, u.username, p.id AS profile_id, p.bio
FROM users u
LEFT JOIN profiles p ON u.profile_id = p.id
WHERE u.id = #{id}
</select>
```

12. **collection标签：** collection 标签用于将查询结果中的多个记录映射到一个集合中，适用于一对多的关系。

```xml
<!-- 定义 OrderItem 结果映射 -->
<resultMap type="OrderItem" id="OrderItemResult">
    <result property="id" column="item_id"/>
    <result property="productId" column="product_id"/>
    <result property="quantity" column="quantity"/>
</resultMap>

        <!-- 定义 Order 结果映射，并关联 OrderItem 列表 -->
<resultMap type="Order" id="OrderWithItemsResult">
<result property="id" column="order_id"/>
<result property="userId" column="user_id"/>
<collection property="items" ofType="OrderItem" resultMap="OrderItemResult"/>
</resultMap>

        <!-- 查询语句 -->
<select id="getOrderWithItems" parameterType="int" resultMap="OrderWithItemsResult">
SELECT o.id AS order_id, o.user_id, oi.id AS item_id, oi.product_id, oi.quantity
FROM orders o
LEFT JOIN order_items oi ON o.id = oi.order_id
WHERE o.id = #{id}
</select>
```

13. **discriminator标签：** discriminator 标签用于根据某个字段的值来选择不同的结果映射，适用于处理继承关系或不同类型的数据。

```xml
<!-- 定义 Animal 基类结果映射 -->
<resultMap type="Animal" id="AnimalResult">
    <result property="id" column="id"/>
    <result property="name" column="name"/>
    <discriminator javaType="String" column="type">
        <case value="cat" resultType="Cat">
            <result property="meowVolume" column="meow_volume"/>
        </case>
        <case value="dog" resultType="Dog">
            <result property="barkVolume" column="bark_volume"/>
        </case>
    </discriminator>
</resultMap>

        <!-- 查询语句 -->
<select id="getAnimalById" parameterType="int" resultMap="AnimalResult">
SELECT id, name, type, meow_volume, bark_volume
FROM animals
WHERE id = #{id}
</select>
```

13. **cache标签：** cache 标签用于配置 MyBatis 的二级缓存，可以提高查询性能。

```xml

<mapper namespace="com.example.mapper.UserMapper">
    <!-- 启用二级缓存 -->
    <cache/>

    <!-- 其他配置 -->
</mapper>
```

## MyBatis 缓存机制

### MyBatis 提供了一级缓存和二级缓存来提高查询性能。下面详细介绍这两种缓存的区别和使用方法。

1. 一级缓存（SqlSession 级别缓存）

* **特点：**
    * **作用范围：** 仅限于当前的 SqlSession。
    * **生命周期：** 当 SqlSession 关闭或提交时，一级缓存会被清空。
    * **默认开启：** 一级缓存是默认开启的，无需额外配置。
    * **工作原理：** 在同一个 SqlSession 中，如果执行相同的查询语句且参数相同，则会从缓存中获取结果，而不会再次访问数据库。
* **示例：**

```java
SqlSession session = sqlSessionFactory.openSession();
try{
UserMapper mapper = session.getMapper(UserMapper.class);

// 第一次查询，会访问数据库
User user1 = mapper.getUserById(1);

// 第二次查询，不会访问数据库，直接从一级缓存中获取
User user2 = mapper.getUserById(1);
}finally{
        session.

close(); // 关闭 SqlSession 后，一级缓存被清空
}
```

2. 二级缓存（Mapper 级别缓存）

* **特点：**
    * 作用范围： 作用于整个应用程序，多个 SqlSession 可以共享同一个二级缓存。
    * 生命周期： 二级缓存的生命周期与 SqlSessionFactory 的生命周期相同，通常在整个应用运行期间一直存在。
    * 需要显式配置： 需要在 mapper.xml 文件中启用，并且可以在 mybatis-config.xml 中进行全局配置。
    * 工作原理： 如果启用了二级缓存，当一个 SqlSession 执行查询后，结果会被存储在二级缓存中。后续其他 SqlSession
      执行相同的查询时，可以从二级缓存中获取结果，而不必再次访问数据库。

* **配置步骤**

1. 在 mybatis-config.xml 中启用全局缓存：

```xml

<configuration>
    <!-- 其他配置 -->
    <settings>
        <setting name="cacheEnabled" value="true"/>
    </settings>
</configuration>

```

2. 在 mapper.xml 中启用二级缓存：

```xml

<mapper namespace="com.example.mapper.UserMapper">
    <!-- 启用二级缓存 -->
    <cache/>

    <!-- 其他配置 -->
</mapper>

```

3. 确保实体类实现了 Serializable 接口：

```java
public class User implements Serializable {
    private int id;
    private String username;
    private String password;
    private String email;

    // Getters and Setters
}
```

4. 使用二级缓存的查询示例：

```java
SqlSession session1 = sqlSessionFactory.openSession();
try{
UserMapper mapper1 = session1.getMapper(UserMapper.class);

// 第一次查询，会访问数据库并将结果存入二级缓存
User user1 = mapper1.getUserById(1);
}finally{
        session1.

close();
}

SqlSession session2 = sqlSessionFactory.openSession();
try{
UserMapper mapper2 = session2.getMapper(UserMapper.class);

// 第二次查询，不会访问数据库，直接从二级缓存中获取
User user2 = mapper2.getUserById(1);
}finally{
        session2.

close();
}
```

### 缓存刷新策略

* 为了保证数据的一致性，MyBatis 提供了多种缓存刷新策略：
    * 手动刷新： 使用 clearCache() 方法手动清除缓存。
    * 自动刷新： 当执行插入、更新或删除操作时，MyBatis 会自动刷新缓存。
    * 配置刷新间隔： 可以通过配置 flushInterval 来设置缓存的刷新时间间隔。

```xml

<cache flushInterval="60000"/> <!-- 每分钟刷新一次 -->
```

### 总结

* 一级缓存： 作用于单个 SqlSession，默认开启，适用于短生命周期的查询优化。
* 二级缓存： 作用于整个应用程序，需要显式配置，适用于跨 SqlSession 的查询优化。
  通过合理配置和使用 MyBatis 的缓存机制，可以显著提高应用程序的性能，减少数据库的访问次数。

## mybatis将多表联查的结果映射到复杂的对象关系（如一对多或一对一）。

### 1. 创建数据库表

* 首先，创建两个表：MesProductSchedule 和 MesProductScheduleMaterial。

```sql
CREATE TABLE MesProductSchedule
(
    id               INT PRIMARY KEY,
    plan_id          INT,
    order_id         INT,
    material_id      INT,
    bom_id           INT,
    product_line_id  INT,
    process_id       INT,
    project_id       INT,
    require_quantity INT,
    product_quantity INT,
    product_date     DATE,
    delivery_date    DATE,
    remark           VARCHAR(255),
    status           VARCHAR(50),
    receive_status   VARCHAR(50),
    serial_no        VARCHAR(50)
);

CREATE TABLE MesProductScheduleMaterial
(
    id               INT PRIMARY KEY,
    schedule_id      INT,
    material_id      INT,
    require_quantity INT,
    usage_quantity   INT,
    left_quantity    INT,
    remark           VARCHAR(255),
    FOREIGN KEY (schedule_id) REFERENCES MesProductSchedule (id)
);

```

### 2. 创建实体类

```java
public class MesProductSchedule {
    private int id;
    private int planId;
    private int orderId;
    private int materialId;
    private int bomId;
    private int productLineId;
    private int processId;
    private int projectId;
    private int requireQuantity;
    private int productQuantity;
    private Date productDate;
    private Date deliveryDate;
    private String remark;
    private String status;
    private String receiveStatus;
    private String serialNo;
    private List<MesProductScheduleMaterial> mesProductScheduleMaterialList;

    // Getters and Setters
}

public class MesProductScheduleMaterial {
    private int id;
    private int scheduleId;
    private int materialId;
    private int requireQuantity;
    private int usageQuantity;
    private int leftQuantity;
    private String remark;

    // Getters and Setters
}
```

### 3. 创建 Mapper XML 文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.example.mapper.MesProductScheduleMapper">

    <!-- 定义MesProductSchedule结果集映射 -->
    <resultMap type="MesProductSchedule" id="MesProductScheduleResult">
        <result property="id" column="id"/>
        <result property="planId" column="plan_id"/>
        <result property="orderId" column="order_id"/>
        <result property="materialId" column="material_id"/>
        <result property="bomId" column="bom_id"/>
        <result property="productLineId" column="product_line_id"/>
        <result property="processId" column="process_id"/>
        <result property="projectId" column="project_id"/>
        <result property="requireQuantity" column="require_quantity"/>
        <result property="productQuantity" column="product_quantity"/>
        <result property="productDate" column="product_date"/>
        <result property="deliveryDate" column="delivery_date"/>
        <result property="remark" column="remark"/>
        <result property="status" column="status"/>
        <result property="receiveStatus" column="receive_status"/>
        <result property="serialNo" column="serial_no"/>
    </resultMap>

    <!-- 定义包含MesProductScheduleMaterial列表的MesProductSchedule结果集映射 -->
    <resultMap id="MesProductScheduleMesProductScheduleMaterialResult" type="MesProductSchedule"
               extends="MesProductScheduleResult">
        <collection property="mesProductScheduleMaterialList" notNullColumn="id" javaType="java.util.List"
                    resultMap="MesProductScheduleMaterialResult"/>
    </resultMap>

    <!-- 定义MesProductScheduleMaterial结果集映射 -->
    <resultMap type="MesProductScheduleMaterial" id="MesProductScheduleMaterialResult">
        <result property="id" column="material_id"/>
        <result property="scheduleId" column="schedule_id"/>
        <result property="materialId" column="material_id"/>
        <result property="requireQuantity" column="require_quantity"/>
        <result property="usageQuantity" column="usage_quantity"/>
        <result property="leftQuantity" column="left_quantity"/>
        <result property="remark" column="remark"/>
    </resultMap>

    <!-- 查询MesProductSchedule及其关联的MesProductScheduleMaterial列表 -->
    <select id="getMesProductScheduleById" parameterType="int"
            resultMap="MesProductScheduleMesProductScheduleMaterialResult">
        SELECT
        mps.id,
        mps.plan_id,
        mps.order_id,
        mps.material_id,
        mps.bom_id,
        mps.product_line_id,
        mps.process_id,
        mps.project_id,
        mps.require_quantity,
        mps.product_quantity,
        mps.product_date,
        mps.delivery_date,
        mps.remark,
        mps.status,
        mps.receive_status,
        mps.serial_no,
        mpsm.id AS material_id,
        mpsm.schedule_id,
        mpsm.material_id,
        mpsm.require_quantity,
        mpsm.usage_quantity,
        mpsm.left_quantity,
        mpsm.remark
        FROM MesProductSchedule mps
        LEFT JOIN MesProductScheduleMaterial mpsm ON mps.id = mpsm.schedule_id
        WHERE mps.id = #{id}
    </select>
</mapper>

```

### 总结

* 主表链接子表外键，查询出来后，过滤行中字段，将符合映射关系的字段映射到实体类中，
  子表同样如此，因为我们已经配置了映射关系，所以当过滤到子表中的字段时会映射到主表实体类中的属性中
* **注意：** 字段映射错位错误，主表和子表都有相同的字段名，当查询时mybatis不知道当前字段是主表还是子表的，就会字段映射错位错误，
* **解决办法：** 设置别名，主表`id as id` ，子表`id as children_id`，然后使用配置好映射关系就解决了。

## 总结

MyBatis 是一个灵活且强大的持久层框架，通过简单的配置和映射，可以有效地管理数据库操作。

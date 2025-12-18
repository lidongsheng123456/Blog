---
title: 冷知识
date: 2025/01/02
tags:
  - 冷知识
categories:
  - 其他
---

## 文件直接在本地用file协议打开和使用Web服务http协议的主要区别在于协议和功能实现上的差异。‌

### 一.协议差异‌：

* 直接在浏览器打开‌：这种方式使用的是file协议，即将HTML文件视为本地资源进行访问。这种方式的请求过程类似于在资源管理器中打开文件，纯粹是本地文件访问，没有网络交互‌。

*

使用Web服务‌：通过HTTP协议访问HTML文件，需要在本地架设一个Web服务器（如使用Python的http.server模块），然后通过浏览器访问localhost上的服务。这种方式有网络交互，能够模拟线上环境，避免了一些因协议不同导致的问题，如跨域请求的差异‌。

### 二.功能实现差异‌：

* 直接在浏览器打开‌：这种方式下，浏览器对资源的请求和处理方式与通过Web服务访问时有所不同。例如，直接调用file协议打开图片的功能可能会被浏览器的安全设置阻挡，因为从安全性角度考虑，服务端不能对客户端进行本地操作‌。

* 使用Web服务‌：通过Web服务访问时，可以更好地模拟线上环境，避免因协议不同导致的一些功能差异。例如，图片等资源的请求会相对本地服务器的根路径进行，而不是本地文件的绝对路径‌。

### 三.安全性和性能差异‌：

* 直接在浏览器打开‌：这种方式没有网络交互，安全性较高，但功能受限。某些需要服务器端处理的功能（如跨域请求）无法实现‌。

* 使用Web服务‌：通过Web服务访问可以更好地处理网络请求和跨域问题，但可能会占用更多的服务器资源，尤其是在处理复杂交互和数据验证时‌。

## 关于浏览器的同源策略

1. 同源策略的定义

* 同源策略是浏览器的一个安全功能，用于限制一个源的文档或者脚本如何与另一个源的资源进行交互。这里的 “源”
  是由协议（scheme）、主机（host）和端口（port）来定义的。
* 具体来说，只有当两个 URL
  的协议、主机和端口都完全相同时，才被认为是同源的。例如，
    * http://example.com:8080/a.html和https://example.com:8080/b.html就不是同源的，因为协议不同；
    * http://example.com:8080/a.html和http://example2.com:8080/b.html不是同源的，因为主机不同；
    * http://example.com:8080/a.html和http://example.com:8081/b.html不是同源的，因为端口不同。

2. 分析给定的两个 URL

* 对于http://127.0.0.1:5500/view/explain_info_view.html和http://127.0.0.1:5500/index.html这两个 URL：
    * 它们的协议都是http。
    * 主机都是127.0.0.1。
    * 端口都是 5500。
* 由于这三个部分都相同，所以它们是同源的，符合浏览器的同源策略。这意味着在 JavaScript
  等脚本语言中，这两个页面之间进行资源共享（如通过XMLHttpRequest、fetch等方式共享数据）等操作在同源策略下是允许的，当然前提是没有其他的安全限制或者自定义的安全策略干扰。

后端解决跨域参考：https://www.lidongshen.top/blogs/houduan/SpringBoot.html#%E4%BD%BF%E7%94%A8%E8%BF%87%E6%BB%A4%E5%99%A8%E5%AE%9E%E7%8E%B0%E8%B7%A8%E5%9F%9F%E9%85%8D%E7%BD%AE%E7%B1%BB

## 处理vue项目部署到nginx之后路由跳转404

* `try_files $uri $uri/ /index.html last` 是一个非常有用的指令，特别是在处理单页应用程序（如Vue.js、React等）时。这个指令确保当请求的文件或目录不存在时，Nginx会返回
  index.html 文件，从而使前端路由能够正常工作。

## 网站SEO优化：提升网站搜索引擎排名

原文链接：https://blog.csdn.net/universsky2015/article/details/135798267

## 在Java中，如果方法的参数是一个接口，通常可以传递接口的实现类实例、匿名内部类实例或Lambda表达式。

1. **匿名内部类**：如果Lambda表达式不适用，可以使用匿名内部类来实现接口。

```java
  power ->{
double consume = power.getPowerConsume() == null ? 0 : power.getPowerConsume();
double totalConsume = power.getTotalPowerConsume() == null ? 0 : power.getTotalPowerConsume();
double carbon = consume * ratio;
double totalCarbon = totalConsume * ratio;
      power.

setCarbonEmission(carbon);
      power.

setTotalCarbonEmission(totalCarbon);
  }
```

可以改写为匿名内部类：

```java
new PowerProcessor() {
    @Override
    public void process (VMesEquipmentPowerDetail power){
        double consume = power.getPowerConsume() == null ? 0 : power.getPowerConsume();
        double totalConsume = power.getTotalPowerConsume() == null ? 0 : power.getTotalPowerConsume();
        double carbon = consume * ratio;
        double totalCarbon = totalConsume * ratio;
        power.setCarbonEmission(carbon);
        power.setTotalCarbonEmission(totalCarbon);
    }
}
```

2. **方法引用**：如果已经有一个方法实现了接口中的抽象方法，可以直接使用方法引用来传递。

```java
this::processPower
```

其中processPower是一个已经定义好的方法，签名与接口方法一致。

3. **实现类实例**：如果接口有具体的实现类，可以直接传递实现类的实例。

```java
new ConcretePowerProcessor()
```

其中ConcretePowerProcessor是实现了接口的具体类。

## 数据库组合父子关系数据 算法实现构造树型结构数据

### js实现

```javascript
// 1.data包含id当前节点，parentId父节点
// 2.每条数据都可能是父节点与子节点与跟节点
// 3.双层循环实现节点的父子关系结构，每次二次循环过滤出一层循环的子节点时都通过对象的引用改变形参data的children。
// 4.如过第一层循环的节点是根节点(根节点默认父节点id为0)，则返回，不然会有一堆烂七八糟的的数据返回，
// 5.循环完所有时，根节点的所有子节点已经完成过滤，从而实现树型结构数据
// 关键点：father.children 每次判断为父子关系时都通过对象引用改变data的值，然后下一次循环时就是基于过滤后的父子关系再次过滤父子关系直到当前节点是根节点(prentId == 0)
handleTrees(data){
    return data.filter(father => {
        let branchArr = data.filter(children => {
            return father.id === children.parentId
        })
        father.children = branchArr.length > 0 ? branchArr : undefined
        // 过滤出根节点
        return father.parentId === 0
    })
}
```

### java实现

```java
public void test(List<MyClass> list) {
    List<MyClass> fatherArr = list.stream().filter(myClass -> {
        List<MyClass> branchArr = list.stream().filter(myClass1 -> {
            return myClass.getId().equals(myClass1.getParentId());
        }).collect(Collectors.toList());

        myClass.setChildren(!branchArr.isEmpty() ? branchArr : null);

        return myClass.getParentId() == 0;
    }).collect(Collectors.toList());

    fatherArr.forEach(System.out::println);
}
```

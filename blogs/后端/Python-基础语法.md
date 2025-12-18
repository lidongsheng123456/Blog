---
title: Python-基础语法
date: 2025/01/01
tags:
  - Python
categories:
  - 后端
---

# Python基础

## 前期准备

### 一.安装运行环境,官网：https://www.python.org/

Python官网下载的Python环境包含以下主要组件‌：

1. Python解释器‌：这是Python的核心组件，负责执行Python代码。用户可以从Python官网下载不同版本的Python解释器，目前主流的是Python
   3.x版本‌。

2. 标准库‌：Python的标准库提供了一系列内置的模块和函数，涵盖了从文本处理到网络通信等多种功能‌。

3. 第三方库‌：除了标准库，Python还依赖于大量的第三方库来支持各种特定的任务和应用程序开发。这些库可以通过包管理工具如pip进行安装和管理‌。

4. 包管理器‌：Python使用包管理器来安装、更新、卸载和管理第三方库。最常用的包管理器是pip，它与Python标准库一起分发‌。

### 二.pip的使用

* pip 是一个现代的，通用的Python包管理工具。提供了对 Python 包的查找、下载、安装、卸载的功能，便
  于我们对Python的资源包进行管理，在安装Python时，会自动下载并且安装pip，

* 默认情况下pip使用的是国外的镜像，下载速度较慢，以下常用的国内镜像源网址：

    1. 清华大学：```https://pypi.tuna.tsinghua.edu.cn/simple```
    2. 阿里云：```http://mirrors.aliyun.com/pypi/simple/```
    3. 中国科技大学：```https://pypi.mirrors.ustc.edu.cn/simple/```
    4. 华中理工大学：```http://pypi.hustunique.com/```
    5. 山东理工大学：```http://pypi.sdutlinux.org/ ```

* 命令行临时使用来下载：```pip install ipython -i https://pypi.mirrors.ustc.edu.cn/simple/```

* 命令提示符指令换源：```pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple```

### 三.JetBrains官网安装PyCharm

* 官网：```https://www.jetbrains.com.cn/```

* 使用要么破解要么申请面向学生和教师的个人许可证

## Python 基础语法

### 一.变量和数据类型

* 变量赋值

```Python
x = 10           # 整数
y = 3.14         # 浮点数
name = "Alice"   # 字符串
is_valid = True  # 布尔值
```

* 数据类型转换

```Python
str_num = str(123)       # 整数转字符串
int_num = int("456")     # 字符串转整数
float_num = float("7.89")# 字符串转浮点数
```

### 二.控制结构

* 条件语句

```Python
if x > 0:
    print("x 是正数")
elif x < 0:
    print("x 是负数")
else:
    print("x 是零")
```

* 循环语句

```Python
for i in range(5):
    print(i)
```

* while 循环

```Python
count = 0
while count < 5:
    print(count)
    count += 1
```

* 列表推导式

```Python
squares = [x**2 for x in range(10)] # 使用列表推导式 [x**2 for x in range(10)] 生成一个包含0到9的平方数的列表。
print(squares)  # 输出: [0, 1, 4, 9, 16, 25, 36, 49, 64, 81]
```

### 三.函数定义

* 定义函数

```Python
def greet(name): # def 是 Python 中定义函数的关键字
    return f"Hello, {name}!" # f前缀允许你在字符串中嵌入表达式，并且这些表达式会被即时求值并替换为它们的结果。

print(greet("Alice"))  # 输出: Hello, Alice!
```

* 带默认参数的函数

```Python
def greet(name="World"): # def 是 Python 中定义函数的关键字
    return f"Hello, {name}!" # f前缀允许你在字符串中嵌入表达式，并且这些表达式会被即时求值并替换为它们的结果。

print(greet())         # 输出: Hello, World!
print(greet("Alice"))  # 输出: Hello, Alice!
```

* 带可变参数的函数

```Python
def sum_numbers(*args):
    print(args)  # 输出: (1, 2, 3, 4)
    return sum(args)

print(sum_numbers(1, 2, 3, 4))  # 输出: 10
```

### 四.数据结构

* 列表 (List)

```Python
my_list = [1, 2, 3, 4, 5]
print(my_list[0])  # 输出: 1
my_list.append(6)  # 添加元素
```
* 元组 (Tuple)

  **特性:**

    * 不可变性：元组一旦创建，其内容不能被修改。这意味着你不能添加、删除或修改元组中的元素。
    * 性能优势：由于元组是不可变的，它们在某些情况下比列表更高效，尤其是在处理大量数据时。
    * 保护数据：如果你有一组数据不希望被修改，使用元组可以确保数据的安全性。
    * 作为字典的键：元组可以作为字典的键，而列表不可以。

```Python
my_tuple = (1, 2, 3)
print(my_tuple[0])  # 输出: 1
```

* 字典 (Dictionary)

```Python
my_dict = {"name": "Alice", "age": 25}
print(my_dict["name"])  # 输出: Alice
my_dict["city"] = "Beijing"  # 添加键值对
```

* 集合 (Set)

```Python
my_set = {1, 2, 3, 4, 5}
print(3 in my_set)  # 输出: True
my_set.add(6)       # 添加元素
```

### 五.文件操作

* 读取文件

```Python
with open('data.txt', 'r', encoding='utf-8') as file:
    content = file.read()
    print(content)
```

* 写入文件

```Python
with open('output.txt', 'w', encoding='utf-8') as file:
    file.write("Hello, World!")
```

### 六.异常处理

* 捕获异常

```Python
try:
    result = 10 / 0
except ZeroDivisionError as e:
    print(f"除零错误: {e}")
finally:
    print("无论如何都会执行")
```

### 七.模块与包

* 导入模块

```Python
import math
print(math.sqrt(16))  # 输出: 4.0
```

* 自定义模块
    * 创建一个名为 mymodule.py 的文件，内容如下：
    ```Python
    def say_hello(name):
        return f"Hello, {name}!"
    ```
    * 在另一个文件中使用该模块：
    ```Python
    import mymodule
    print(mymodule.say_hello("Alice"))  # 输出: Hello, Alice!
    ```

### 八.类与对象

* 类与对象

```Python
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age
    
    def greet(self):
        return f"Hello, my name is {self.name} and I am {self.age} years old."

person = Person("Alice", 25)
print(person.greet())  # 输出: Hello, my name is Alice and I am 25 years old.
```

## Python基础语法跟java区别

* **语法简洁性：** Python的语法更加简洁，不需要显式的分号结尾，而Java需要。
* **类型声明：** Python是动态类型语言，变量无需声明类型；Java是静态类型语言，变量必须声明类型。
* **代码块定义：** Python使用缩进来定义代码块，而Java使用大括号{}。
* **类定义：** Python类定义使用class关键字，方法不需要显式声明返回类型；Java类定义也需要class关键字，但方法需要声明返回类型。
* **包管理：** Python使用import语句导入模块，Java使用import语句导入包。

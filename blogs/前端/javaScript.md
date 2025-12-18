---
title: javaScript
date: 2023/12/15
tags:
  - 前端三剑客
categories:
  - 前端
---

JavaScript 是一种广泛使用的编程语言，主要用于网页开发。本文档将介绍一些常用的 JavaScript API、数据类型、DOM 操作、事件处理和其他功能。

## 基础语法

### 变量声明

- **`var`**：旧版变量声明
- **`let`**：块级作用域变量
- **`const`**：常量

```javascript
var x = 10;
let y = 20;
const z = 30;
```

### 数据类型

- **基本类型**：`number`, `string`, `boolean`, `null`, `undefined`, `symbol`
- **引用类型**：`object`, `array`, `function`

```javascript
let num = 10;
let str = "Hello";
let bool = true;
let n = null;
let u = undefined;
let sym = Symbol("key");
let obj = {name: "John", age: 30};
let arr = [1, 2, 3];
let func = function () {
    console.log("Hello");
};
```

### 控制结构

- **条件语句**：`if`, `else if`, `else`
- **循环**：`for`, `while`, `do...while`
- **开关语句**：`switch`

```javascript
if (x > 0) {
    console.log("Positive");
} else if (x < 0) {
    console.log("Negative");
} else {
    console.log("Zero");
}
for (let i = 0; i < 5; i++) {
    console.log(i);
}
let i = 0;
while (i < 5) {
    console.log(i);
    i++;
}
let j = 0;
do {
    console.log(j);
    j++;
} while (j < 5);
switch (x) {
    case 1:
        console.log("One");
        break;
    case 2:
        console.log("Two");
        break;
    default:
        console.log("Other");
}
```

## 函数

### 声明函数

- **函数声明**
- **函数表达式**

```javascript
function greet(name) {
    console.log(Hello, $
    {
        name
    }
    !
)
    ;
}

const sayHi = function (name) {
    console.log(Hi, $
    {
        name
    }
    !
)
    ;
};
```

### 箭头函数

箭头函数提供了更简洁的语法。

```javascript
const add = (a, b) => a + b;
```

### 默认参数

可以在函数声明中设置默认参数值。

```javascript
function multiply(a, b = 1) {
    return a * b;
}
```

### 剩余参数

可以使用剩余参数收集多个参数。

```javascript
function sum(...args) {
    return args.reduce((acc, curr) => acc + curr, 0);
}
```

## 对象

### 创建对象

- **对象字面量**
- **构造函数**
- **类**

```javascript
const person = {
    name: "John", age: 30, greet: function () {
        console.log(Hello, $
        {
            this.name
        }
        !
    )
        ;
    }
};

function Person(name, age) {
    this.name = name;
    this.age = age;
    this.greet = function () {
        console.log(Hello, $
        {
            this.name
        }
        !
    )
        ;
    };
}

class Person {
    constructor(name, age) {
        this.name = name;
        this.age = age;
    }

    greet() {
        console.log(Hello, $
        {
            this.name
        }
        !
    )
        ;
    }
}
```

### 访问和修改属性

```javascript
const person = {name: "John", age: 30};
console.log(person.name); // John person.age = 31; 
console.log(person.age); // 31
```

## 数组

### 创建数组

```javascript
const arr = [1, 2, 3];
const fruits = ["apple", "banana", "cherry"];
```

### 数组方法

- **遍历**：`forEach`, `map`, `filter`, `reduce`
- **查找**：`find`, `indexOf`, `includes`
- **添加和删除**：`push`, `pop`, `shift`, `unshift`, `splice`

```javascript
const numbers = [1, 2, 3, 4, 5];
numbers.forEach(num => console.log(num));
const doubled = numbers.map(num => num * 2);
console.log(doubled); // [2, 4, 6, 8, 10]
const even = numbers.filter(num => num % 2 === 0);
console.log(even); // [2, 4]
const sum = numbers.reduce((acc, curr) => acc + curr, 0);
console.log(sum); // 15
const found = numbers.find(num => num > 3);
console.log(found); // 4
const index = numbers.indexOf(3);
console.log(index); // 2
const includes = numbers.includes(5);
console.log(includes); // true
numbers.push(6);
console.log(numbers); // [1, 2, 3, 4, 5, 6]
numbers.pop();
console.log(numbers); // [1, 2, 3, 4, 5]
numbers.shift();
console.log(numbers); // [2, 3, 4, 5]
numbers.unshift(1);
console.log(numbers); // [1, 2, 3, 4, 5]
numbers.splice(2, 1);
console.log(numbers); // [1, 2, 4, 5]
```

## DOM 操作

### 选择元素

- **`getElementById`**
- **`getElementsByClassName`**
- **`getElementsByTagName`**
- **`querySelector`**
- **`querySelectorAll`**

```javascript
const element = document.getElementById("myId");
const elements = document.getElementsByClassName("myClass");
const elements2 = document.getElementsByTagName("div");
const element3 = document.querySelector(".myClass");
const elements4 = document.querySelectorAll(".myClass");
```

### 修改元素

- **`innerHTML`**
- **`textContent`**
- **`setAttribute`**
- **`removeAttribute`**

```javascript
element.innerHTML = "<p>Hello, World!</p>";
element.textContent = "Hello, World!";
element.setAttribute("class", "newClass");
element.removeAttribute("class");
```

### 添加和删除元素

- **`appendChild`**
- **`removeChild`**
- **`insertBefore`**

```javascript
element.innerHTML = "<p>Hello, World!</p>";
element.textContent = "Hello, World!";
element.setAttribute("class", "newClass");
element.removeAttribute("class");
```

## 事件处理

### 添加事件监听器

- **`addEventListener`**
- **`removeEventListener`**

```javascript
const button = document.getElementById("myButton");
button.addEventListener("click", function () {
    console.log("Button clicked!");
});
button.removeEventListener("click", function () {
    console.log("Button clicked!");
});
```

### 事件对象

- **`event`**

```javascript
button.addEventListener("click", function (event) {
    console.log(event.type); // click 
    console.log(event.target); // <button id="myButton">Click me</button>
});
```

## 异步编程

### 回调函数

```javascript
function fetchData(callback) {
    setTimeout(() => {
        callback({data: "Some data"});
    }, 1000);
}

fetchData(data => {
    console.log(data); // { data: "Some data" }
});
```

### Promises

```javascript
function fetchData(callback) {
    setTimeout(() => {
        callback({data: "Some data"});
    }, 1000);
}

fetchData(data => {
    console.log(data); // { data: "Some data" } 
});
```

### Async/Await

```javascript
async function fetchData() {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            resolve({data: "Some data"});
        }, 1000);
    });
}

async function getData() {
    try {
        const data = await fetchData();
        console.log(data); // { data: "Some data" }
    } catch (error) {
        console.error(error);
    }
}

getData();
```

## 其他功能

### 模块

- **ES6 模块**

```javascript
// math.js export 
function add(a, b) {
    return a + b;
}

export function subtract(a, b) {
    return a - b;
}

// main.js 
import {add, subtract} from "./math.js";

console.log(add(1, 2)); // 3 
console.log(subtract(5, 3)); // 2
```

### 错误处理

- **`try...catch`**

```javascript
try { // 可能会抛出错误的代 
    throw new Error("An error occurred");
} catch (error) {
    console.error(error.message); // An error occurred
} finally {
    console.log("This will always run");
}
```

### 类型转换

- **隐式转换**
- **显式转换**

```javascript
let num = "10";
let result = num * 1; // 隐式转换，结果为 10
num = "10";
result = Number(num); // 显式转换，结果为 10
```

## for...in 和 for...of

* for...in
    * 应用场景：
        * 遍历对象的可枚举属性。
        * 当你需要访问对象的所有键（包括继承的可枚举属性）时使用。
    * 注意事项：
        * 不建议用于数组，因为会遍历所有可枚举属性，包括可能添加到 Array.prototype 上的方法。

* for...of
    * 应用场景：
        * 遍历可迭代对象（如数组、字符串、Map、Set 等）的值。
        * 当你需要直接获取集合中的元素值时使用。
        * 支持解构赋值，可以更方便地操作复杂数据结构。
    * 注意事项：
        * 只能遍历具有迭代协议的对象，不能直接遍历普通对象的属性。

* **示例代码**

```javascript
// 使用 for...in 遍历对象属性
const obj = {a: 1, b: 2, c: 3};
for (let key in obj) {
    console.log(key, obj[key]); // 输出：a 1, b 2, c 3
}

// 使用 for...of 遍历数组元素
const arr = [1, 2, 3];
for (let value of arr) {
    console.log(value); // 输出：1, 2, 3
}
```

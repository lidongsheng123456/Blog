---
title: 深入JavaScript的异步请求
date: 2024/12/29
tags:
  - javaScript
categories:
  - 前端
---

# JavaScript的http异步请求

## 早期 JavaScript 实现异步请求的主要方法是使用 XMLHttpRequest 对象

### 一.实现原理

* 基于回调：XMLHttpRequest 是一个内置的浏览器对象，用于与服务器进行异步通信。它通过回调函数来处理异步操作的结果。
* 事件监听：通过监听 readystatechange 事件，可以知道请求的状态变化。当请求完成时，可以通过检查 readyState 和 status
  属性来处理响应数据。
* 兼容性：XMLHttpRequest 在所有现代浏览器中都得到了支持，包括 Internet Explorer 5.0 及更高版本。

### 二.API

```javascript
// 1.创建 XMLHttpRequest 对象
var xhr = new XMLHttpRequest();

// 2.配置请求
// 第一个参数是请求方法（如 GET、POST）。
// 第二个参数是请求的 URL。
// 第三个参数是一个布尔值，表示请求是否异步（true 表示异步）。
xhr.open('GET', '/user?ID=12345', true);

// 3.设置回调函数
// readyState 属性表示请求的状态：
// 0：未初始化。
// 1：已打开。
// 2：已发送。
// 3：正在接收。
// 4：完成。
// status 属性表示 HTTP 状态码（如 200 表示成功）。
xhr.onreadystatechange = function () {
    if (xhr.readyState === 4 && xhr.status === 200) {
        // 请求成功，处理响应数据
        console.log(xhr.responseText);
    } else if (xhr.readyState === 4) {
        // 请求失败，处理错误
        console.error('请求失败，状态:', xhr.status);
    }
};

// 4.发送请求
// 对于 GET 请求，通常不需要发送数据，因此可以传入 null 或不传参数。
// 对于 POST 请求，可以在 send 方法中传入请求体数据：
// xhr.send(JSON.stringify({
//   firstName: 'Fred',
//   lastName: 'Flintstone'
// }));
xhr.send();
```

## Promise

### 一.简介

* 现代Javascript中，Promise是处理异步操作的主要方式之一,介绍一下。
* Promise 是 JavaScript 中处理异步操作的一种机制，它提供了一种更优雅的方式来处理回调地狱（callback
  hell）问题，并且使得异步代码的编写和阅读更加直观。以下是 Promise 实现异步的关键原因：

### 二.Promise实现异步关键

1. 状态管理（Promise 有三种状态）

    * Pending（进行中）：初始状态，既不是成功也不是失败。
    * Fulfilled（已成功）：操作成功完成，Promise 被解析为一个值。
    * Rejected（已失败）：操作失败，Promise 被拒绝并返回一个错误。

   这种状态管理使得我们可以明确地知道异步操作的进展，并在不同状态下执行不同的逻辑。

2. 链式调用

    * Promise 支持链式调用，即可以在 .then()、.catch() 和 .finally() 方法中继续返回新的
      Promise。这使得多个异步操作可以按顺序执行，而不需要嵌套多层回调函数。

    ```javascript
      axios.get('/api/data')
        .then(response => {
            console.log(response.data);
            return axios.get('/api/more-data');
        })
        .then(response => {
            console.log(response.data);
        })
        .catch(error => {
            console.error('Error:', error);
        });
    ```

3. 非阻塞特性

    * JavaScript 是单线程的，但 Promise 的设计允许异步操作在后台执行，而不阻塞主线程。当异步操作完成时，Promise
      会将相应的回调函数加入事件循环的任务队列中，等待主线程空闲时执行。

4. 与 async 和 await 的结合

    * 虽然 Promise 是底层机制，但它与 async 和 await 结合使用时，可以使异步代码看起来像同步代码，从而简化了异步编程。

   ```javascript
      async function fetchData() {
        try {
            const response = await axios.get('/api/data');
            console.log(response.data);
        } catch (error) {
            console.error('Error:', error);
        };
      }
   ```

### 三.Promise 实现异步的具体机制

1. 创建 Promise
    * 当你创建一个新的 Promise 时，传入的执行器函数（executor）会立即执行。该函数接收两个参数：resolve 和 reject，用于改变
      Promise 的状态。
2. 注册回调函数
    * 使用 .then() 注册成功回调，使用 .catch() 注册失败回调。这些回调函数会在 Promise 状态改变时被调用。
3. 状态改变
    * 当异步操作完成时，调用 resolve 或 reject 来改变 Promise 的状态。状态一旦改变，就不能再改变。
4. 事件循环
    * JavaScript 的事件循环会检查任务队列中的微任务（microtasks），并在主线程空闲时执行这些任务。Promise 的回调函数就是微任务的一部分。
5. .then() 回调方法
    * .then()中的形参代表着前面相关异步操作成功返回并传递过来的结果，方便后续基于这个结果做进一步的逻辑处理等操作。
      不过要注意，如果前面的异步函数（或者回调函数里）没有显式返回一个值，那么传递给后续 .then 回调函数里对应的形参可能就是
      undefined 了。
6. .catch() 回调方法
    * .catch()方法用于捕获前面任何一个.then()
      方法中抛出的错误。它的参数（error）代表在前面的异步操作（someAsynchronousFunction、anotherAsynchronousFunction或者yetAnotherAsynchronousFunction）中出现的错误信息,如果第一个异步请求返回是一个被拒绝（rejected）的
      Promise，那么这个拒绝状态会沿着.then()链向下传播，直到被.catch()捕获。类似于java的RunTimeException不处理会一直往上抛直到虚拟机处理
7. .finally()  回调方法
    * 不管前面的 Promise
      操作（也就是那些.then里的异步操作、或者是出现了错误被.catch捕获的情况）最终是成功（resolved）状态还是失败（rejected）状态，finally块中的代码都会被执行。它通常用于执行一些清理操作，比如关闭之前打开的网络连接、清理定时器等，无论整个异步操作流程最终结果如何这些清理动作都需要进行。

示例：使用 Promise 实现异步请求

```javascript
function fetchData(url) {
    return new Promise((resolve, reject) => {
        // 模拟异步请求
        setTimeout(() => {
            if (Math.random() > 0.5) {
                resolve({data: 'Success'});
            } else {
                reject(new Error('Request failed'));
            }
        }, 1000);
    });
}

fetchData('/api/data')
    .then(response => console.log(response))
    .catch(error => console.error(error));
```

## async 和 await

### 一.简介

* async 和 await 并不是为了让异步请求变成同步代码，而是为了简化异步代码的编写和阅读，使异步代码看起来更像同步代码。它们的核心目的是提高代码的可读性和维护性，而不是改变
  JavaScript 的单线程、异步非阻塞特性。

### 二.异步与同步的区别

* 同步代码：按顺序执行，每一步必须等待上一步完成才能继续。如果某一步是耗时操作（如网络请求），整个程序会阻塞，直到该操作完成。

```javascript
  // 同步代码示例
console.log('Step 1');
const result = someSynchronousFunction(); // 阻塞在这里，直到函数返回
console.log('Step 2', result);
```

* 异步代码：不会阻塞主线程，可以在后台执行耗时操作，主线程可以继续执行其他任务。当异步操作完成时，再处理结果。

```javascript
  // 异步代码示例
console.log('Step 1');
someAsynchronousFunction().then(result => {
    console.log('Step 2', result);
});
console.log('Step 3'); // 这行代码不会等待 Step 2 完成
```

### 三.async 和 await 的作用

* async 函数：声明一个函数为异步函数，允许在函数内部使用 await 关键字。async 函数总是返回一个 Promise。
* await 表达式：用于等待一个 Promise 解析，并暂停当前函数的执行，直到 Promise 解析或拒绝。await 只能在 async 函数中使用。

```javascript
// 使用 async 和 await 的异步代码
async function fetchData() {
    console.log('Step 1');
    const result = await someAsynchronousFunction(); // 暂停这里，直到 Promise 解析
    console.log('Step 2', result);
}

console.log('Step 3'); // 这行代码不会等待 fetchData 完成
```

* 在这个例子中：
    * Step 1 和 Step 2 之间的代码看起来像是同步的，但实际上 await 只是暂停了 fetchData 函数的执行，而不会阻塞主线程。
    * Step 3 会在 Step 1 之后立即执行，不会等待 Step 2。

### 四.为什么使用 async 和 await？

* 简化代码结构：避免回调地狱（callback hell），使代码更加直观和易读。
* 错误处理更简单：可以使用 try...catch 块来捕获异步操作中的错误，而不是嵌套多层 .catch()。
* 链式调用更清晰：多个异步操作可以按顺序书写，而不必通过 .then() 链式调用来实现。

```javascript
// 使用 async 和 await 的错误处理
async function fetchData() {
    try {
        console.log('Step 1');
        const result = await someAsynchronousFunction();
        console.log('Step 2', result);
    } catch (error) {
        console.error('Error:', error);
    }
}
```

### 五.JavaScript 的单线程特性

* 即使使用了 async 和 await，JavaScript 仍然是单线程的，不会因为 await 而阻塞整个程序。await 只是暂停了当前 async
  函数的执行，让出控制权给事件循环，允许其他任务继续执行。

* 示例：结合 async 和 await 的异步请求

```javascript
async function sendMessage() {
    if (this.userInput.trim() === '') return;

    const userMessage = {type: 'user', content: 'You: ' + this.userInput, createdAt: new Date()};
    this.messages.push(userMessage);

    try {
        const {read} = await chat({userId: this.userId, message: this.userInput});
        let currentMessage = {type: 'robot', content: 'Robot: ', createdAt: new Date()};
        this.messages.push(currentMessage);

        let chunk = await read();
        while (!chunk.done) {
            currentMessage.content += chunk.value;
            this.$set(this.messages, this.messages.length - 1, currentMessage); // 更新最后一条消息
            chunk = await read();
        }
    } catch (error) {
        this.messages.push({type: 'error', content: 'Error: ' + error.message, createdAt: new Date()});
    } finally {
        this.userInput = '';
        this.scrollToBottom();
    }
}
```

* 在这个例子中：
    * sendMessage 是一个 async 函数，使用 await 等待 chat 请求完成。
    * await 暂停了 sendMessage 函数的执行，但不会阻塞主线程，允许其他任务继续执行。
    * 错误处理通过 try...catch 实现，代码结构清晰易读。

### 六.总结

* async 和 await 不是将异步代码变成同步代码，而是提供了一种更优雅的方式来编写和处理异步操作。
* 它们简化了代码结构，提高了可读性和维护性，同时保留了 JavaScript 的异步非阻塞特性。
* 使用 async 和 await 可以更好地管理异步流程，避免回调地狱，并简化错误处理。

## fetch

### 摘自CSDN用户：软考鸭

### 简介

* fetch是浏览器内置的API‌，用于发送网络请求。它是浏览器内置的全局JavaScript方法，无需下载安装，可以直接使用。

* 在Web开发中，fetch
  API已成为处理HTTP请求的一种现代且强大的方式。作为XMLHttpRequest的升级版，fetch不仅简化了代码，还提供了更丰富的功能和更灵活的使用方式。本文将全面讲解fetch函数的定义、参数及返回值，帮助开发者更好地理解和应用这一API。

### fetch跟axios区别

1. 流式处理：
    * fetch 提供了对流式响应的支持，可以通过 response.body.getReader() 读取流数据。这对于处理聊天消息流（例如，服务器发送的事件或实时消息）非常有用。
    * axios 默认情况下不直接支持流式处理，虽然可以通过一些额外的配置和库来实现，但不如 fetch 简单直接。
2. 原生支持：
    * fetch 是浏览器原生支持的 API，不需要额外引入第三方库，这可以减少项目的依赖。
    * axios 是一个第三方库，需要通过 npm 或 yarn 安装，增加了项目的依赖管理复杂性。
3. 灵活性：
    * fetch 提供了更底层的控制，允许开发者更灵活地处理请求和响应。
    * axios 提供了更高级的抽象和默认处理，适合快速开发，但在某些复杂场景下可能不够灵活。

### 一.fetch函数定义

* fetch函数是Web API的一部分，用于在JavaScript脚本中发起HTTP请求。其基本语法如下：

```javascript
fetch(input, {init})
    .then(response => {
        // 处理响应
    })
    .catch(error => {
        // 处理错误
    });
```

* ```input```：定义要获取的资源。它可以是一个USVString（包含要获取资源的URL）或一个```Request```对象。
* ```init```（可选）：一个配置项对象，包含所有对请求的设置，如请求方法、头信息、请求体等。

fetch函数返回一个Promise对象，该对象在请求成功时解析（resolve）为一个Response对象，在请求失败时拒绝（reject）一个错误。

### 二、fetch参数详解

1. input参数

* input参数可以是字符串形式的URL或Request对象。当使用URL字符串时，fetch默认发起GET请求。使用Request对象时，可以更灵活地配置请求，包括请求方法、头信息、请求体等。

2. init参数

* init参数是一个可选的配置项对象，支持以下属性：

    * method：请求使用的方法，如GET、POST、PUT、DELETE等。
    * headers：请求的头信息，形式为Headers对象或包含ByteString值的对象字面量。
    * body：请求的body信息，可能是一个Blob、BufferSource、FormData、URLSearchParams或USVString对象。注意，GET或HEAD方法的请求不能包含body信息。
    * mode：请求的模式，如cors、no-cors或same-origin。
    * credentials：请求的credentials，如omit、same-origin或include。
    * cache：请求的cache模式，如default、no-store、reload、no-cache、force-cache或only-if-cached。
    * redirect：重定向模式，如follow（自动重定向）、error（如果产生重定向将自动终止并抛出错误）或manual（手动处理重定向）。
    * referrer：一个USVString，可以是no-referrer、client或一个URL。
    * referrerPolicy：指定referer HTTP头部的值。
    * integrity：请求的subresource integrity值。

### 三、fetch返回值

* fetch函数返回一个Promise对象，该对象在请求成功时解析为一个Response对象。Response对象包含了一系列属性和方法，用于处理响应数据。

* **Response对象的主要属性和方法**

    * status（number）：HTTP请求结果的状态码，范围在100-599之间。

    * statusText（String）：服务器返回的状态报告。

    * ok（boolean）：如果状态码在200-299之间，表示请求成功，则为true。

    * headers（Headers）：返回头部信息，可通过Headers对象的方法访问。

    * url（String）：请求的URL。

    * text()：返回一个解析为文本字符串的Promise对象。

    * json()：返回一个解析为JSON对象的Promise对象。

    * blob()：返回一个解析为Blob对象的Promise对象。

    * arrayBuffer()：返回一个解析为ArrayBuffer对象的Promise对象。

    * formData()：返回一个解析为FormData对象的Promise对象。

### 四、使用实例

* **GET请求示例**

* 获取HTML类型数据：

```javascript
fetch('/users.html')
    .then(response => response.text())
    .then(body => {
        document.body.innerHTML = body;
    })
    .catch(error => console.error('Error:', error));

```

* 获取JSON类型数据：

```javascript
fetch('/users.json')
    .then(response => response.json())
    .then(json => console.log('parsed json', json))
    .catch(error => console.error('Error:', error));
```

* **POST请求示例**

```javascript
fetch('/login', {
    method: 'POST',
    headers: {
        'Content-Type': 'application/json',
    },
    body: JSON.stringify({
        username: 'example',
        password: '123456',
    }),
})
    .then(response => response.json())
    .then(data => console.log('Success:', data))
    .catch((error) => console.error('Error:', error));
```

### 五、总结

fetch
API提供了比XMLHttpRequest更简洁、更强大的HTTP请求处理能力。通过了解其定义、参数和返回值，开发者可以更加灵活地在Web应用中发起和处理HTTP请求。无论是GET请求还是POST请求，fetch都提供了一种简洁且高效的方式来获取和解析响应数据。希望本文能帮助你更好地理解和应用fetch
API。

fetch原文链接：https://blog.csdn.net/gusushantang/article/details/141533162

## 回调地狱（Callback Hell）及其解决方案

### 一.什么是回调地狱？

* 回调地狱是指在编写异步代码时，由于嵌套多层回调函数而导致代码难以阅读和维护的情况。每个异步操作完成后，需要通过回调函数来处理结果，而多个异步操作的嵌套会导致代码结构变得非常复杂和难以理解。

* **示例：回调地狱**

```javascript
function fetchData() {
    someAsynchronousFunction(function (result1) {
        anotherAsynchronousFunction(result1, function (result2) {
            yetAnotherAsynchronousFunction(result2, function (result3) {
                console.log('Final result:', result3);
            });
        });
    });
}
```

* 在这个例子中，每一层异步操作都依赖于前一层的结果，导致代码层层嵌套，难以维护。

### 二.什么是回调地狱？

* 异步操作的依赖性：当多个异步操作之间存在依赖关系时，必须等待前一个操作完成才能执行下一个操作。
* 错误处理的复杂性：每层回调都需要单独处理错误，增加了代码的复杂度。
* 代码可读性差：嵌套的回调函数使得代码难以阅读和理解，尤其是在逻辑复杂的情况下。

### 三.如何解决回调地狱？

1. 使用 Promise

    * Promise 提供了一种更优雅的方式来处理异步操作，避免了回调地狱。通过 .then() 和 .catch() 方法，可以链式调用多个异步操作，使代码更加简洁和易读。

    * **示例：使用 Promise**

```javascript
function fetchData() {
    someAsynchronousFunction()
        .then(result1 => anotherAsynchronousFunction(result1))
        .then(result2 => yetAnotherAsynchronousFunction(result2))
        .then(result3 => console.log('Final result:', result3))
        .catch(error => console.error('Error:', error));
}
```

2. 使用 async 和 await

    * async 和 await 是基于 Promise 的语法糖，进一步简化了异步代码的编写。它们允许你以同步的方式编写异步代码，同时保持非阻塞特性。

    * **示例：使用 async 和 await**

```javascript
async function fetchData() {
    try {
        const result1 = await someAsynchronousFunction();
        const result2 = await anotherAsynchronousFunction(result1);
        const result3 = await yetAnotherAsynchronousFunction(result2);
        console.log('Final result:', result3);
    } catch (error) {
        console.error('Error:', error);
    }
}
```

3. 总结

* 回调地狱 是由于嵌套多层回调函数导致的代码难以阅读和维护的问题。
* Promise 提供了一种更优雅的方式来处理异步操作，避免了回调地狱。
* async 和 await 进一步简化了异步代码的编写，使代码看起来像同步代码，同时保持非阻塞特性。
* 在 Vue 组件中，合理使用 async 和 await 可以显著提高代码的可读性和维护性，避免回调地狱问题。

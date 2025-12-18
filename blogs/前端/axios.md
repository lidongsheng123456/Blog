---
title: axios
date: 2024/03/20
tags:
  - axios
categories:
  - 前端
---

## Axios 是一个基于 promise 的 HTTP 库，可以用于浏览器和 node.js。

## 基本用法

### 发送 GET 请求

```javascript
axios.get('https://api.example.com/data').then(response => {
    console.log(response.data);
}).catch(error => {
    console.error(error);
});
```

### 发送 POST 请求

```javascript
axios.post('https://api.example.com/data', {firstName: 'John', lastName: 'Doe'}).then(response => {
    console.log(response.data);
}).catch(error => {
    console.error(error);
});
```

## 配置请求

### 设置默认配置

```javascript
axios.defaults.baseURL = 'https://api.example.com';
axios.defaults.headers.common['Authorization'] = 'Bearer YOUR_TOKEN';
axios.defaults.headers.post['Content-Type'] = 'application/x-www-form-urlencoded';
```

### 单个请求配置

```javascript
axios.get('/user', {params: {ID: 12345}}).then(function (response) {
    console.log(response);
}).catch(function (error) {
    console.log(error);
});
```

## 使用axios统一请求响应配置

### 构造新的axios实例

```javascript
import axios from 'axios'
import router from "@/router";

//使用axios的构造函数初始化baseURL（根路径）timeout（超时时间）
const request = axios.create({
    baseURL: process.env.VUE_APP_BASEURL,//引用.env.development文件
    timeout: 30000
})
```

### 请求拦截器

```javascript
//使用请求拦截器
//统一加token
request.interceptors.request.use(config => {
    //设置请求体中的数据是json格式发送，并指定字符编码为utf-8避免乱码
    config.headers['Content-Type'] = 'application/json;charset=utf-8';
    //获取存储在本地的token令牌
    let user = JSON.parse(localStorage.getItem('xm-user')) || {}
    //给请求头加上token属性
    config.headers['token'] = user.token

    console.log('请求拦截器打印：', config)

    return config
}, error => {
    console.log(error)
    //Promise 是 JavaScript 中处理异步操作的一种机制，代表了一个异步操作的最终完成（或失败）及其结果值。
    return Promise.reject(error)
})
```

### 响应拦截器

```javascript
//使用响应拦截器
//统一返回结果
request.interceptors.response.use(response => {
        let res = response.data;

        //兼容服务端返回的字符串数据
        if (typeof res === 'string') {
            //如果res有值则转成js的对象没值则返回原来数据
            res = res ? JSON.parse(res) : res
        }

        // 401 token令牌效验失败
        if (res.code === '401') {
            router.push('/login')
        }

        console.log('响应拦截器打印：', response)

        return res
    },
    error => {
        console.log(error)
        //Promise 是 JavaScript 中处理异步操作的一种机制，代表了一个异步操作的最终完成（或失败）及其结果值。
        return Promise.reject(error)
    }
)
```

## 错误处理

```javascript
axios.get('/user/12345').then(function (response) {
    console.log(response);
}).catch(function (error) {
    if (error.response) {
        // 请求已发出，但服务器响应的状态码不在 2xx 范围内
        console.log(error.response.data);
        console.log(error.response.status);
        console.log(error.response.headers);
    } else { // 发生在设置请求时触发的错误 
        console.log(error.message);
    }
});
```

## 取消请求

```javascript
const CancelToken = axios.CancelToken;
const source = CancelToken.source();
axios.get('/user/12345', {cancelToken: source.token}).catch(function (thrown) {
    if (axios.isCancel(thrown)) {
        console.log('请求取消', thrown.message);
    } else {
        // 处理错误 
    }
});
// 取消请求 
source.cancel('用户取消操作。');
```

## 并发请求

```javascript
function getUserAccount() {
    return axios.get('/user/12345');
}

function getUserPermissions() {
    return axios.get('/user/12345/permissions');
}

axios.all([getUserAccount(), getUserPermissions()]).then(axios.spread(function (acct, perms) { // 两个请求现在都已完成 
}));
```

## 其他功能

- **转换请求/响应数据**：可以自定义请求或响应的数据转换。
- **超时设置**：可以为请求设置超时时间。
- **自定义适配器**：可以自定义适配器以支持不同的环境。


---
title: React
date: 2025/07/18
tags:
  - React
categories:
  - 前端
---

## 关于虚拟 DOM

1. 本质是一个 Object 类型的变量
2. 虚拟 DOM 比较“轻”，因为即没有真实 DOM 的更多属性也没有渲染到浏览器页面，真实 DOM 比较重，因为相比较虚拟的多很多属性，比如：
   id，innerHTML，innerText
3. 虚拟 DOM 的更新比真实 DOM 更快，因为虚拟 DOM 更新时，会先创建一个虚拟 DOM，然后通过比较新旧虚拟 DOM，得出差异，最后将差异渲染到真实 DOM

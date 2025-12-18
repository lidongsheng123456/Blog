---
title: html
date: 2023/10/10
tags:
  - 前端三剑客
categories:
  - 前端
---

## 基础结构

### HTML 文档的基本结构

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document Title</title></head>
<body><h1>Main Heading</h1>
<p>This is a paragraph.</p></body>
</html>
```

## 常用标签

### 文本标签

- **标题**：`<h1>` 到 `<h6>`
- **段落**：`<p>`
- **强调**：`<strong>` 和 `<em>`
- **文本格式**：`<b>`, `<i>`, `<u>`, `<s>`

```html
<h1>Main Heading</h1> <p>This is a <strong>bold</strong> and <em>italic</em> text.</p> <p><b>Bold</b>, <i>Italic</i>,
    <u>Underline</u>, <s>Strikethrough</s></p>
```

### 列表

- **无序列表**：`<ul>`
- **有序列表**：`<ol>`
- **列表项**：`<li>`

```html

<ul>
    <li>Item 1</li>
    <li>Item 2</li>
    <li>Item 3</li>
</ul>
<ol>
    <li>First Item</li>
    <li>Second Item</li>
    <li>Third Item</li>
</ol>
```

### 链接

- **超链接**：`<a>`

```html
<a href="https://example.com">Visit Example</a>
```

### 图像

- **图像**：`<img>`

```html
<!--
    img标签会默认发起get请求到src对应的路径
    当浏览器通过 <img> 标签请求图片资源时，图片数据会被下载到浏览器
    的内存中，并显示在页面上。具体来说：
        1.浏览器缓存：浏览器会将图片资源缓存到本地磁盘缓存中，以便后续请求时可以直接从缓存中读取，减少网络延迟。
        2.内存：浏览器还会将图片数据加载到内存中，以便快速渲染和显示在页面上。
-->
<img src="image.jpg" alt="Description of image">
```

### 表格

- **表格**：`<table>`
- **行**：`<tr>`
- **单元格**：`<td>` 和 `<th>`

```html

<table>
    <tr>
        <th>Header 1</th>
        <th>Header 2</th>
    </tr>
    <tr>
        <td>Row 1, Cell 1</td>
        <td>Row 1, Cell 2</td>
    </tr>
    <tr>
        <td>Row 2, Cell 1</td>
        <td>Row 2, Cell 2</td>
    </tr>
</table>
```

### 表单

- **表单**：`<form>`
- **输入框**：`<input>`
- **文本区域**：`<textarea>`
- **按钮**：`<button>`

```html

<form action="/submit" method="post">
    <label for="name">Name:</label> <input type="text" id="name" name="name"><br><br>
    <label for="email">Email:</label> <input type="email" id="email" name="email"><br><br>
    <label for="message">Message:</label><br>
    <textarea id="message" name="message"></textarea><br><br>
    <button type="submit">Submit</button>
</form>
```

### 媒体

- **音频**：`<audio>`
- **视频**：`<video>`

```html

<audio controls>
    <source src="audio.mp3" type="audio/mpeg">
    Your browser does not support the audio element.
</audio>
<video width="320" height="240" controls>
    <source src="video.mp4" type="video/mp4">
    Your browser does not support the video element.
</video>
```

### 其他常用标签

- **分段**：`<section>`, `<article>`, `<aside>`, `<header>`, `<footer>`, `<nav>`
- **语义化标签**：`<main>`, `<figure>`, `<figcaption>`

```html

<header>
    <h1>Website Header</h1>
    <nav>
        <ul>
            <li><a href="#home">Home</a></li>
            <li><a href="#about">About</a></li>
            <li><a href="#contact">Contact</a></li>
        </ul>
    </nav>
</header>
<main>
    <section><h2>Section Title</h2>
        <p>Section content goes here.</p></section>
    <article><h2>Article Title</h2>
        <p>Article content goes here.</p></article>
</main>
<footer><p>Copyright © 2023</p></footer>
```

## 属性

### 常用属性

- **`id`**：唯一标识符
- **`class`**：类名
- **`style`**：内联样式
- **`href`**：链接地址
- **`src`**：资源路径
- **`alt`**：替代文本
- **`target`**：目标窗口
- **`type`**：输入类型
- **`value`**：值
- **`placeholder`**：占位符

```html

<div id="main-content" class="content" style="color: blue;">
    <a href="https://example.com" target="_blank">Link</a>
    <img src="image.jpg" alt="Description of image">
    <input type="text" id="name" name="name" placeholder="Enter your name">
</div>
```

## 事件

### 常用事件

- **`onclick`**：点击事件
- **`onchange`**：改变事件
- **`onsubmit`**：提交事件
- **`onload`**：加载事件

```html

<button onclick="alert('Button clicked!')">Click me</button>
<select onchange="console.log(this.value)">
    <option value="1">Option 1</option>
    <option value="2">Option 2</option>
</select>
<form onsubmit="return validateForm()"><input type="text" id="name" name="name">
    <button type="submit">Submit</button>
</form>
<script>
    function validateForm() {
        const name = document.getElementById('name').value;
        if (name === '') {
            alert('Name must be filled out');
            return false;
        }
        return true;
    }
</script>
```

## 其他功能

- **数据属性**：`data-*`
- **隐藏元素**：`hidden`
- **自定义属性**：`custom-attr`

```html

<div data-id="123" custom-attr="value" hidden> This content is hidden.</div>
```

---
title: css
date: 2023/10/20
tags:
  - 前端三剑客
categories:
  - 前端
---

CSS（Cascading Style Sheets）是一种用于控制网页外观的样式表语言。本文档将介绍一些常用的 CSS 属性、选择器、布局方法和其他功能。

## 基础语法

### CSS 规则

CSS 规则由选择器和声明块组成。声明块包含一个或多个声明，每个声明由属性和值组成。

```css 
selector {
    property: value;
}
```

### 示例

```css
p {
    color: blue;
    font-size: 16px;
}
```

## 选择器

### 基本选择器

- **元素选择器**：选择特定类型的元素
- **类选择器**：选择具有特定类的元素
- **ID 选择器**：选择具有特定 ID 的元素

```css
p {
    color: blue;
}

.class-name {
    background-color: yellow;
}

#id-name {
    font-weight: bold;
}
```

### 属性选择器

- **属性存在选择器**：选择具有特定属性的元素
- **属性值选择器**：选择具有特定属性值的元素

```css
input[type="text"] {
    border: 1px solid black;
}

a[target] {
    color: red;
}
```

### 伪类和伪元素

- **伪类**：选择处于特定状态的元素
- **伪元素**：选择元素的特定部分

```css
a:hover {
    color: green;
}

p::first-line {
    font-weight: bold;
}
```

## 常用属性

### 文本样式

- **颜色**：`color`
- **字体大小**：`font-size`
- **字体系列**：`font-family`
- **文本对齐**：`text-align`
- **文本装饰**：`text-decoration`

```css
p {
    color: blue;
    font-size: 16px;
    font-family: Arial, sans-serif;
    text-align: center;
    text-decoration: underline;
}
```

### 盒模型

- **宽度**：`width`
- **高度**：`height`
- **边框**：`border`
- **内边距**：`padding`
- **外边距**：`margin`

```css
div {
    width: 200px;
    height: 100px;
    border: 1px solid black;
    padding: 10px;
    margin: 20px;
}
```

### 背景

- **背景颜色**：`background-color`
- **背景图像**：`background-image`
- **背景重复**：`background-repeat`
- **背景位置**：`background-position`

```css
body {
    background-color: #f0f0f0;
    background-image: url('background.jpg');
    background-repeat: no-repeat;
    background-position: center;
}
```

### 显示和定位

- **显示方式**：`display`
- **定位方式**：`position`
- **浮动**：`float`
- **清除浮动**：`clear`

```css
.container {
    display: flex;
    position: relative;
}

.item {
    float: left;
    clear: both;
}
```

### 布局

- **弹性布局**：`flexbox`
- **网格布局**：`grid`

```css
.flex-container {
    display: flex;
    justify-content: space-between;
    align-items: center;
}

.grid-container {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    grid-gap: 10px;
}
```

## 响应式设计

### 媒体查询

媒体查询允许根据设备特性（如屏幕宽度）应用不同的样式。

```css
@media (max-width: 600px) {
    body {
        background-color: lightblue;
    }
}
```

### 视口单位

- **视口宽度**：`vw`
- **视口高度**：`vh`
- **视口最小尺寸**：`vmin`
- **视口最大尺寸**：`vmax`

```css
css .container {
    width: 80vw;
    height: 50vh;
}
```

## 动画和过渡

### 过渡

过渡用于在一段时间内平滑地改变 CSS 属性。

```css
.button {
    transition: background-color 0.3s ease;
}

.button:hover {
    background-color: red;
}
```

### 动画

动画用于创建更复杂的视觉效果。

```css
@keyframes slidein {
    from {
        margin-left: 100%;
        width: 300%;
    }
    to {
        margin-left: 0%;
        width: 100%;
    }
}

.animated-element {
    animation: slidein 2s forwards;
}
```

## 其他功能

### 变量

CSS 变量允许在样式表中定义可重用的值。

```css
:root {
    --primary-color: blue;
}

p {
    color: var(--primary-color);
}
```

### 自定义属性

自定义属性允许为元素添加自定义数据。

```css
div {
    --custom-attr: "Hello, World!";
}
```

### 嵌套选择器

虽然 CSS 本身不支持嵌套选择器，但可以使用预处理器（如 SASS 或 LESS）来实现。

```css
.container {
    .item {
        color: red;
    }
}
```


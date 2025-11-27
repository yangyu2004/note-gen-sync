# CSS 基础语法与选择器

## 一、CSS 基本语法

### 语法结构

css

```css
选择器 {
  属性: 值;
  属性: 值;
  /* 声明为键值对，可包含多条 */
}
```

* **选择器**：要应用样式的 HTML 标签（所有元素 / 特定元素）。
* **声明**：由 “属性：值” 组成，每条声明以分号结尾。

### 示例

css

```css
p {
  color: #333;        /* 颜色 */
  font-size: 16px;    /* 字体大小 */
  font-family: Arial; /* 字体 */
  text-align: center; /* 对齐方式 */
  line-height: 1.5;   /* 行高 */
}
```

## 二、CSS 引入方式

CSS 有 3 种引入方式，**优先级：内联样式 > 内部样式表 > 外部样式表**（优先级高的样式会覆盖优先级低的）。

### 1. 内联样式

直接写在 HTML 标签的 `style`属性中：

```html
<h1 style="color: red;">这是一级标题（内联样式）</h1>
```

### 2. 内部样式表

写在 `<head>`标签的 `<style>`标签内：

```html
<head>
  <style>
    h2 {
      color: green;
      font-size: 26px;
    }
  </style>
</head>
```

### 3. 外部样式表

通过 `<link>`标签引入独立的 `.css`文件：

```html
<!-- HTML中引入 -->
<head>
  <link rel="stylesheet" href="./css/style.css">
</head>

<!-- style.css文件内容 -->
h3 {
  color: yellow;
  font-size: 26px;
}
```

## 三、完整示例（HTML+CSS）

```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1">
  <meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, user-scalable=no">
  <title>CSS引入方式</title>
  <!-- 外部样式表 -->
  <link rel="stylesheet" href="./css/style.css">
  <!-- 内部样式表 -->
  <style>
    p {
      color: #333;
      font-size: 26px;
      font-family: Arial;
      text-align: center;
      line-height: 1.5;
    }
    h2 {
      color: green;
      font-size: 26px;
    }
  </style>
</head>
<body>
  <!-- 内联样式 -->
  <h1 style="color: red;">这是一级标题（内联样式）</h1>
  <!-- 内部样式 -->
  <h2>这是二级标题（内部样式）</h2>
  <!-- 外部样式 -->
  <h3>这是三级标题（外部样式）</h3>
</body>
</html>
```

## 四、CSS 选择器

选择器用于指定要设置样式的 HTML 元素，常见类型如下：

### 1. 元素选择器

直接选择 HTML 标签：

```css
/* 选择所有<h2>标签 */
h2 {
  color: green;
  font-size: 26px;
}
```

### 2. 类选择器

通过 `class`属性选择元素（可复用）：

```css
/* 选择class="highlight"的元素 */
.highlight {
  background-color: yellow;
}
```

```html
<h3 class="highlight">类选择器演示</h3>
```

### 3. ID 选择器

通过 `id`属性选择元素（唯一，不可复用）：

```css
/* 选择id="header"的元素 */
#header {
  font-size: 36px;
}
```

```html
<h4 id="header">ID选择器演示</h4>
```

### 4. 通用选择器

选择页面所有元素：

```css
* {
  font-family: "kaiti"; /* 所有元素使用楷体 */
}
```

### 5. 子元素选择器

选择父元素的**直接子元素**（用 `>`连接）：

```css
/* 选择class="father"下的直接子元素class="son" */
.father > .son {
  color: yellowgreen;
  font-weight: bold;
}
```

```html
<div class="father">
  <p class="son">子元素选择器演示</p>
</div>
```

### 6. 后代选择器

选择父元素下的**所有后代元素**（用空格连接）：

```css
/* 选择class="parent"下的所有<p>标签 */
.parent p {
  color: brown;
  font-size: 16px;
}
```

```html
<div class="parent">
  <p>后代选择器演示</p>
</div>
```

### 7. 相邻选择器

选择某个元素的**下一个相邻元素**（用 `+`连接）：

```css
/* 选择<h3>后的第一个<p>标签 */
h3 + p {
  background-color: red;
}
```

### 8. 伪类选择器

选择元素的**特定状态**（如 hover）：

```css
/* 鼠标 hover 时的样式 */
#element:hover {
  background-color: red;
}
```

### 9. 伪元素选择器

为元素添加**额外内容 / 样式**（如 `::after`/`::before`）：

```css
/* 在元素后添加内容 */
::after {
  content: "after";
}
```

## CSS常用属性

```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1">
  <meta name="renderer" content="webkit">
  <meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, user-scalable=no">
  <title>CSS常用属性</title>
  <style>
    /* 样式代码区域 */
  </style>
</head>
<body>
  <!-- 页面内容区域 -->
</body>
</html>
```

## 二、CSS 常用基础属性

示例中用到的核心属性：

| 属性名               | 作用             | 示例值              |
| -------------------- | ---------------- | ------------------- |
| `background-color` | 设置背景颜色     | `aqua`/`brown`  |
| `width`            | 设置元素宽度     | `150px`/`100px` |
| `height`           | 设置元素高度     | `100px`/`200px` |
| `font-size`        | 设置字体大小     | `60px`            |
| `display`          | 改变元素显示模式 | `inline`          |

## 三、元素显示模式（重点）

HTML 元素默认分为 **块级元素**、**行内元素**、**行内块元素**，可通过 `display`属性修改。

### 1. 块级元素（`display: block`）

* 独占一行，宽度默认撑满父容器
* 可设置 `width`/`height`
* 示例（代码）：

  ```css
  .block {
    background-color: aqua; /* 背景色：浅蓝 */
    width: 150px;
    height: 100px;
  }
  ```

  ```html
  <div class="block">这是一个块元素</div>
  ```

### 2. 行内元素（`display: inline`）

* 多个元素并排显示，宽度由内容决定
* **不可设置 `width`/`height`**（设置无效）
* 示例（代码）：

  ```css
  .inline {
    background-color: brown; /* 背景色：棕色 */
    width: 200px; /* 无效 */
    height: 200px; /* 无效 */
  }
  ```

  ```html
  <span class="inline">这是一个行内元素</span>
  ```

### 3. 行内块元素（`display: inline-block`）

* 多个元素并排显示，同时可设置 `width`/`height`
* 示例（代码）：

  ```css
  .inline-block {
    width: 100px;
    height: 150px;
  }
  ```

  ```html
  <img src="./logo.png" alt="" class="inline-block">
  <img src="./logo.png" alt="" class="inline-block">
  ```

### 4. 显示模式转换

可通过 `display`属性强制转换元素模式：

```html
<!-- 块元素转成“行内元素” -->
<div style="display:inline;background-color:red">这是一个转为行内的元素</div>
```

## 四、完整代码示例

```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1">
  <meta name="renderer" content="webkit">
  <meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, user-scalable=no">
  <title>CSS常用属性</title>
  <style>
    .block {
      background-color: aqua;
      width: 150px;
      height: 100px;
    }
    .inline {
      background-color: brown;
      width: 200px;
      height: 200px;
    }
    .inline-block {
      width: 100px;
      height: 150px;
    }
  </style>
</head>
<body>
  <h1 style="font-size:60px 'kaiti'">这是一个font-size属性示例</h1>
  <p style="line-height:40px">这是一段长文本长文本长文本</p>
  
  <div class="block">这是一个块元素</div>
  <span class="inline">这是一个行内元素</span>
  
  <img src="./logo.png" alt="" class="inline-block">
  <img src="./logo.png" alt="" class="inline-block">
  <img src="./logo.png" alt="" class="inline-block">
  
  <div style="display:inline;background-color:red">这是一个转为行内的元素</div>
</body>
</html>
```

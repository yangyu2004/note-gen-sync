# CSS 核心基础：语法、引入方式与常用属性

## 一、CSS 基本语法

### 1. 语法结构

CSS 规则由**选择器**和**声明块**组成：

```css
选择器 {
  属性: 值;   /* 声明（键值对），可包含多条 */
  属性: 值;
}
```

* **选择器**：指定要应用样式的 HTML 元素（所有元素 / 特定元素）。
* **声明**：由 “属性：值” 组成，每条声明以分号结尾。

### 2. 基础示例

```css
p {
  color: #333;        /* 文字颜色 */
  font-size: 16px;    /* 字体大小 */
  font-family: Arial; /* 字体类型 */
  text-align: center; /* 文本对齐方式 */
  line-height: 1.5;   /* 行高 */
}
```

## 二、CSS 引入方式

CSS 有 3 种引入方式，**优先级：内联样式 > 内部样式表 > 外部样式表**（高优先级样式会覆盖低优先级）。

### 1. 内联样式

直接写在 HTML 标签的 `style` 属性中（仅作用于当前标签）：

```html
<h1 style="color: red;">这是一级标题（内联样式）</h1>
```

### 2. 内部样式表

写在 `<head>` 内的 `<style>` 标签中（作用于当前页面）：

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

通过 `<link>` 引入独立的 `.css` 文件（可复用，作用于多个页面）：

```html
<!-- HTML 中引入外部样式表 -->
<head>
  <link rel="stylesheet" href="./css/style.css">
</head>

<!-- style.css 文件内容 -->
h3 {
  color: yellow;
  font-size: 26px;
}
```

### 完整示例（HTML+CSS 混合引入）

```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>CSS 引入方式演示</title>
  
  <!-- 外部样式表 -->
  <link rel="stylesheet" href="./css/style.css">
  
  <!-- 内部样式表 -->
  <style>
    p {
      color: #333;
      font-size: 26px;
      text-align: center;
    }
  </style>
</head>
<body>
  <!-- 内联样式 -->
  <h1 style="color: red;">一级标题（内联）</h1>
  <!-- 内部样式 -->
  <h2>二级标题（内部）</h2>
  <!-- 外部样式 -->
  <h3>三级标题（外部）</h3>
</body>
</html>
```

## 三、CSS 选择器

选择器用于定位要设置样式的 HTML 元素，常见类型如下：

### 1. 元素选择器

直接选择 HTML 标签（作用于所有匹配标签）：

```css
/* 选择所有 <h2> 标签 */
h2 {
  color: green;
  font-size: 26px;
}
```

### 2. 类选择器

通过 `class` 属性选择元素（可复用，用 `.` 标识）：

```css
/* 选择所有 class="highlight" 的元素 */
.highlight {
  background-color: yellow;
}
```

```html
<h3 class="highlight">类选择器演示</h3>
```

### 3. ID 选择器

通过 `id` 属性选择元素（唯一，不可复用，用 `#` 标识）：

```css
/* 选择 id="header" 的元素 */
#header {
  font-size: 36px;
}
```

```html
<h4 id="header">ID 选择器演示</h4>
```

### 4. 通用选择器

选择页面所有元素（用 `*` 标识）：

```css
* {
  font-family: "kaiti"; /* 所有元素使用楷体 */
}
```

### 5. 子元素选择器

选择父元素的**直接子元素**（用 `>` 连接）：

```css
/* 选择 .father 的直接子元素 .son */
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
/* 选择 .parent 下的所有 <p> 标签 */
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

选择元素的**下一个相邻元素**（用 `+` 连接）：

```css
/* 选择 <h3> 后的第一个 <p> 标签 */
h3 + p {
  background-color: red;
}
```

### 8. 伪类选择器

选择元素的**特定状态**（如 hover，用 `:` 标识）：

```css
/* 鼠标悬浮时的样式 */
#element:hover {
  background-color: red;
}
```

### 9. 伪元素选择器

为元素添加**额外内容 / 样式**（如 `::after`，用 `::` 标识）：

```css
/* 在元素后插入内容 */
::after {
  content: "后缀内容";
}
```

## 四、CSS 常用属性与元素显示模式

### 1. 常用基础属性

| 属性名               | 作用             | 示例值               |
| -------------------- | ---------------- | -------------------- |
| `background-color` | 设置背景颜色     | `aqua`/`brown`   |
| `width`            | 设置元素宽度     | `150px`/`100px`  |
| `height`           | 设置元素高度     | `100px`/`200px`  |
| `font-size`        | 设置字体大小     | `60px`             |
| `display`          | 改变元素显示模式 | `inline`/`block` |

### 2. 元素显示模式（核心）

HTML 元素默认分为 3 类，可通过 `display` 修改：

#### （1）块级元素（`display: block`）

* 独占一行，宽度默认撑满父容器
* 可设置 `width`/`height`
* 示例：

  ```css
  .block {
    background-color: aqua;
    width: 150px;
    height: 100px;
  }
  ```

  ```html
  <div class="block">这是块元素</div>
  ```

#### （2）行内元素（`display: inline`）

* 多个元素并排，宽度由内容决定
* **不可设置 `width`/`height`**（设置无效）
* 示例：

  ```css
  .inline {
    background-color: brown;
    width: 200px; /* 无效 */
  }
  ```

  ```html
  <span class="inline">这是行内元素</span>
  ```

#### （3）行内块元素（`display: inline-block`）

* 并排显示 + 可设置宽高（结合块级 / 行内元素特点）
* 示例：

  ```css
  .inline-block {
    width: 100px;
    height: 150px;
  }
  ```

  ```html
  <img src="./logo.png" class="inline-block">
  ```

#### （4）显示模式转换

通过 `display` 强制转换元素类型：

```html
<!-- 块元素转成“行内元素” -->
<div style="display:inline; background-color:red">转为行内的元素</div>
```

### 3. 完整代码示例（属性 + 显示模式）

```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <title>CSS 常用属性演示</title>
  <style>
    .block {
      background-color: aqua;
      width: 150px;
      height: 100px;
    }
    .inline {
      background-color: brown;
    }
    .inline-block {
      width: 100px;
      height: 150px;
    }
  </style>
</head>
<body>
  <h1 style="font-size:60px">font-size 属性示例</h1>
  <p style="line-height:40px">这是一段长文本</p>
  
  <div class="block">块元素</div>
  <span class="inline">行内元素</span>
  
  <img src="./logo.png" class="inline-block">
  <img src="./logo.png" class="inline-block">
  
  <div style="display:inline; background-color:red">转换为行内的元素</div>
</body>
</html>
```

## 五、盒子模型

盒子模型是 CSS 布局的核心概念，**所有 HTML 元素都可以看作一个 “盒子”**，浏览器通过这个模型计算元素的最终尺寸、位置及与其他元素的间距。

| 组成部分                    | 作用说明                                                                                                                 |
| --------------------------- | ------------------------------------------------------------------------------------------------------------------------ |
| **内容区（Content）** | 盒子的核心，显示文本、图片等实际内容。通过 `width`/`height` 直接设置其尺寸（默认仅作用于内容区）。                   |
| **内边距（Padding）** | 内容区与边框之间的空白区域，**会继承盒子的背景色**。通过 `padding` 相关属性设置，不影响其他元素。                |
| **边框（Border）**    | 包裹内边距和内容区的线条，可设置样式、宽度、颜色。通过 `border` 相关属性设置，会增加盒子总尺寸。                       |
| **外边距（Margin）**  | 盒子与其他元素之间的空白区域，**透明无背景**，用于控制元素间距。通过 `margin` 相关属性设置，不影响盒子自身尺寸。 |

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>CSS盒子模型</title>
    <style>
        .demo {
            background-color: lightblue;
            display: inline-block;
            border: 5px solid blue;
        }
        .border-demo {
            width: 300px;
            height: 200px;
            border-style: solid;
            border-color: red;
            border-width: 15px 5px 25px 35px;/* 上 右 下 左 */
            border-style: solid dashed dotted double;/* solid是实线 dashed是虚线 dotted是点线 double是双线 */
            padding: 20px;
            margin: 15px;
            background-color: lightgreen;
        }
    </style>
</head>
<body>
    <div class="demo">B站罗大佑</div>
    <div class="border-demo">这是一个边框示例</div>
</body>
</html>
```

## 六、浮动

# CSS 选择器与样式

## 1. 基本语法

CSS 的基本语法如下：

```
选择器 { 声明 }
```

- **选择器**：用于指定要应用样式的 HTML 元素。
- **声明**：由属性和值组成，属性和值之间用冒号 `:` 分隔，多条声明之间用分号 `;` 分隔。

### 示例：

```css
p {
    color: #333;                /* 颜色 */
    font-size: 16px;             /* 字号 */
    font-family: Arial;          /* 字体 */
    text-align: center;          /* 对齐方式 */
    line-height: 1.5;            /* 行高 */
}
```

## 2. 引入方式

CSS 样式可以通过以下方式引入：

### (1) 内联样式

直接在 HTML 元素的 `style` 属性中定义样式。

**示例：**

```html
<h1 style="color:red;">这是一个一级标题，使用内联样式</h1>
```

### (2) 内部样式表

将 CSS 代码写在 HTML 文件的 `<style>` 标签中。

**示例：**

```html
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
```

### (3) 外部样式表

将 CSS 代码保存为 `.css` 文件，然后通过 `<link>` 标签引入。

**示例：**

```html
<link rel="stylesheet" href="css/style.css">
```

## 3. 样式优先级

CSS 样式的优先级从高到低如下：

1. **内联样式**（直接写在 HTML 元素中）
2. **内部样式表**（写在 `<style>` 标签中）
3. **外部样式表**（通过 `<link>` 引入的 CSS 文件）

**注意**：优先级高的样式会覆盖优先级低的样式。

## 4. 选择器类型

### (1) 元素选择器（Element Selector）

选择特定的 HTML 元素。

**示例：**

```css
p {
    /* 为所有 <p> 元素设置样式 */
}
```

### (2) ID 选择器（ID Selector）

通过 `#` 符号指定 ID，选择唯一的一个元素。

**示例：**

```css
#myHeading {
    /* 为 ID 为 myHeading 的元素设置样式 */
}
```

### (3) 类选择器（Class Selector）

通过 `.` 符号指定类名，选择具有该类名的元素。

**示例：**

```css
.myClass {
    /* 为所有 class 为 myClass 的元素设置样式 */
}
```

### (4) 通用选择器（Universal Selector）

用 `*` 选择所有元素。

**示例：**

```css
* {
    /* 为所有元素设置样式 */
}
```

## 5. 样式与选择器的使用

你可以将样式写在 `<style>` 标签中，也可以通过 `style` 属性直接写在 HTML 元素上。

### 示例 HTML 文件：

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1">
    <meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, user-scalable=no">
    <title>CSS 引入方式</title>
    <link rel="stylesheet" href="css/style.css">
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
    <h1 style="color:red;">这是一个一级标题，使用内联样式</h1>
    <h2>这是一个二级标题，使用内部样式</h2>
    <h3>这是一个三级标题，使用外部样式</h3>
</body>
</html>
```

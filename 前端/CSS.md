CSS常用属性

```
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1">
    <meta name="renderer" content="webkit">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1, user-scalable=no">
    <title>CSS常用属性</title>
    <style>
        .block {
            background-color: aqua;
            width: 150px;
            height: 100px;
        }
        .inline {
            background-color: brown;
            height: 200px;
        }
        .inline-block {
            width: 100px;
            height: 150px;
            display: inline-block; /* 补充 display 属性 */
        }
    </style>
</head>
<body>
    <h1 style="font-size: 60px;">这是一个font-size属性展示例</h1>
    <p style="font-size: 20px;">这是一个p标签文字大小</p>
    <div class="block">这是一个块元素</div>
    <span class="inline">这是一个行内元素</span>
    <img src="./logo.png" alt="" class="inline-block">
    <div class="inline-block">这是一个转换为行内的元素</div>
    <div style="display: inline; background-color: red;">这是一个转换为行内的元素</div>
</body>
</html>
```

---
title: 获取带有jQuery的AJAX的函数返回值
categories:
- Web前端
tags:
- jQuery
- 异步加载
- AJAX
toc: true
---
<Excerpt in index | > 
本文讲述如何正确写带有jQuery AJAX的函数返回值<!-- more -->
<The rest of contents | 余下全文>

# 错误代码
无法正确获取返回值的代码：
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <link rel="stylesheet" href="https://cdn.staticfile.org/twitter-bootstrap/3.3.7/css/bootstrap.min.css">
    <script src="https://cdn.staticfile.org/jquery/2.1.1/jquery.min.js"></script>
    <script src="https://cdn.staticfile.org/twitter-bootstrap/3.3.7/js/bootstrap.min.js"></script>
    <title>Title</title>
</head>
<body>

<div align="center">
    <button class="btn btn-primary" id="btn">获取本地txt文件</button>
</div>

<script>

    $("#btn").on("click", function () {
        var data = load_txt();
        alert(data);
    });

    function load_txt(){

        $.ajax({
            url: "input.txt",
            method:"GET",
            dataType:"json",

            success: function (data) {
                return data;	// 这样返回，不对
            }
        });
    }

</script>
</body>
</html>
```
结果如下图：
![错误实例](https://raw.githubusercontent.com/poptensory/OnlineResource/master/wrong.gif "错误实例")

# 正确代码
修改两个地方：1.async:false. 2.用函数内部的变量接收ajax加载的数据后返回.
代码就能正确加载正确，代码如下：
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <link rel="stylesheet" href="https://cdn.staticfile.org/twitter-bootstrap/3.3.7/css/bootstrap.min.css">
    <script src="https://cdn.staticfile.org/jquery/2.1.1/jquery.min.js"></script>
    <script src="https://cdn.staticfile.org/twitter-bootstrap/3.3.7/js/bootstrap.min.js"></script>
    <title>Title</title>
</head>
<body>

<div align="center">
    <button class="btn btn-primary" id="btn">获取本地txt文件</button>
</div>

<script>

    $("#btn").on("click", function () {
        var data = load_txt();
        alert(data);
    });

    function load_txt(){
        var res = null;  // 定义“全局”变量
        $.ajax({
            url: "input.txt",
            method:"GET",
            dataType:"json",
            async:false,    // 同步加载
            success: function (data) {
                // //return data;
                res = data.toString();  // 存储结果
            }
        });
        return res; // 此时返回
    }

</script>
</body>
</html>
```
结果如下图：

![正确结果](https://raw.githubusercontent.com/poptensory/OnlineResource/master/right.gif "Optional title")

# 其他问题
jQuery+ajax加载本地txt，txt内容只能为数字？
---
title: jQuery+bootstrap翻页
categories:
- Web前端
tags:
- jQuery
- bootstrap
- 前端
toc: true
---
<Excerpt in index | > 
jQuery+bootstrap翻页<!-- more -->
<The rest of contents | 余下全文>
翻页功能实现如下：
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title><link rel="stylesheet" href="https://cdn.staticfile.org/twitter-bootstrap/3.3.7/css/bootstrap.min.css">
    <script src="https://cdn.staticfile.org/jquery/2.1.1/jquery.min.js"></script>
    <script src="https://cdn.staticfile.org/twitter-bootstrap/3.3.7/js/bootstrap.min.js"></script>
</head>
<body>
    <ul class="pagination">
        <li class="arrow left"><a>&laquo</a></li>
        <li class="active"><a>1</a></li>
        <li><a>2</a></li>
        <li><a>3</a></li>
        <li><a>4</a></li>
        <li><a>5</a></li>
        <li><a>6</a></li>
        <li class="arrow right"><a>&raquo</a></li>
    </ul>


    <script>
        // 点击翻页，not过滤掉左右箭头
        $('li').not(".arrow").click(function () {
            $(".active").removeClass("active");
            $(this).addClass("active");
        });

        // 点击左箭头
        $("li.left").click(function () {

            var x = $(".active");

            if(x.prev().attr("class")!='arrow left'){
                x.removeClass("active");
                x.prev().addClass("active");
            }
        });

        // 点击右箭头
        $("li.right").click(function () {
            var x = $(".active");
            if(x.next().attr("class")!='arrow right'){
                x.removeClass("active");
                x.next().addClass("active");
            }
        });
    </script>
</body>
</html>
```
效果如下：
![分页](https://raw.githubusercontent.com/poptensory/OnlineResource/master/2018-10-15_14-19-16.gif "Optional title")
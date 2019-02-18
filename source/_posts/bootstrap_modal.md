---
title: bootstrap模态框
categories:
- Web前端
tags:
- bootstrap
- 模态框
- JavaScript
toc: true
---
<Excerpt in index | > 
jQuery+bootstrap翻页<!-- more -->
<The rest of contents | 余下全文>
模态框及其激活实现如下：
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <link rel="stylesheet" href="https://cdn.staticfile.org/twitter-bootstrap/3.3.7/css/bootstrap.min.css">
    <script src="https://cdn.staticfile.org/jquery/2.1.1/jquery.min.js"></script>
    <script src="https://cdn.staticfile.org/twitter-bootstrap/3.3.7/js/bootstrap.min.js"></script>
</head>
<body>

<div>
    <h2>模态框</h2>
    <!--两种方式激活模态框，第二种更加可控-->
    <!--<button class="btn btn-success" data-toggle="modal" data-target="#myModal">创建模态框data-target</button>-->
    <button class="btn btn-success" data-toggle="modal" id="create_modal">创建模态框-点击事件</button>
</div>

<br>
<div>
    <button id="add_row" class="btn btn-info">添加行</button>
</div>

<br>

<div class="col-lg-2">
    <table class="table table-striped table-hover table-condensed table-bordered table-responsive">
        <thead>
        <tr>
            <th>#</th>
            <th>First Name</th>
        </tr>
        </thead>
        <tbody id="tb1">
        <tr>
            <td>1</td>
            <td>Anna</td>
        </tr>
        <tr>
            <td>2</td>
            <td>Tommy</td>
        </tr>
        <tr>
            <td>3</td>
            <td>Mica</td>
        </tr>
        </tbody>
    </table>
</div>




<!--模态框的设定-->
<div class="modal fade" id="myModal" tabindex="-1" role="dialog" aria-labelledby="myModalLabel" aria-hidden="true">
    <div class="modal-dialog">
        <div class="modal-content">

            <div class="modal-header">
                <button type="button" class="close" data-dismiss="modal" aria-hidden="true">&times;</button>
                <h4 class="modal-title" id="myModalLabel">模态框（Modal）标题</h4>
            </div>

            <div class="modal-body">
                <p>主体内容填在这里</p>
                学号<input type="text" id="stuno">
                姓名<input type="text" id="name">
            </div>

            <div class="modal-footer">
                <button type="button" class="btn btn-default" data-dismiss="modal">关闭</button>
                <button type="button" class="btn btn-primary" id="submit_change">提交更改</button>
            </div>

        </div>
    </div>
</div>



<script>

    $("#create_modal").on('click', function () {
        //$("#myModal").modal('show');
        $('#myModal').modal({backdrop: 'static', keyboard: true});
    });


    // 提交按钮的点击事件
    $("#submit_change").click(function () {

        var name = $("#name").val();
        var stuno = $("#stuno").val();

        if(name=='' || stuno == ''){
            alert("姓名或学号不能为空");
            return;
        }

        $("tbody#tb1").append('<tr>\n' +
            '<td>'+stuno+'</td>\n' +
            '<td>'+name+'</td>\n' +
            '</tr>');
        $("#myModal").modal('hide');
    });

    // 添加行 按钮的点击事件
    $("#add_row").click(function () {
        // backdrop: 空白处不关闭， keyboard: true， ESC键可以关闭模态框
        $('#myModal').modal({backdrop: 'static', keyboard: false});
        $("#name").val('');
        $("#stuno").val('');
    });
    
    // $("#myModal").on('hide.bs.modal', function () {
    //     alert("我要消失了哦");
    // });
    //
    // $("#myModal").on('hidden.bs.modal', function () {
    //     alert("我不见了");
    // });

</script>

</body>
</html>
```
效果如下：
![模态框](https://raw.githubusercontent.com/poptensory/OnlineResource/master/2018-10-15_20-23-30.gif "Optional title")
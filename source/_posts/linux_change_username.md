---
title: Linux修改用户名和主机名
categories:
- Linux
tags:
- Ubuntu
- 用户管理
toc: true
---
<Excerpt in index | > 
本文讲如何修改Linxu在用户名和主机名<!-- more -->
<The rest of contents | 余下全文>

# 主机名和用户名
在Linux(Ubuntu)终端可以看到A@B前后一行。@前面的A叫用户名，@后面B的叫主机名。一个主机名可以对应多个用户。
# 修改主机名
修改主机名需要修改两个文件的配置
```
sudo vi /etc/hostname
```
这里存的就是主机名。
然后，
```
sudo vi /etc/hosts
```
修改里面的本机ip地址对应的主机名

修改完两个文件之后，重启即可发现主机名修改了。

# 修改用户名
1. Ctrl+D多按几次，退出所有用户的登录(包括root用户和要修改用户名的用户)，不然会提示当前用户被占用
2. 用root用户登录
3. 下面代码修改
```
usermod -l new_usename old_username
```
4.Ctrl+D退出root用户登录，然后old_username无法实现登录了，用new_usename可以登录。

# 注意
1. Xshell等远程工具用root登录Ubuntu的时候，好像要配置一些文件。
2. 修改用户名之后，还没修改用户主目录。
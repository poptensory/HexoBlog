---
title: Linux用户管理
categories:
- Linux
tags:
- Linux
- 用户管理
- 运维
toc: true
---
<Excerpt in index | > 
本文记录用户管理和组管理的常用命令<!-- more -->
<The rest of contents | 余下全文>
`Linux`中存在很多组，很多组是在系统安装过程中自动创建的。
组与用户可以是多对多关系，即一个组包含多个用户，一个用户可以属于多个组。
系统安装过程中，有一个`root`用户，有一个自建用户(例如我的，`tensory`)，该用户在创建的时候，会自动添加到多个组。

`tensory`组：与用户名相同的组

`sudo组`：该组内用户可以执行`sudo`命令，从而具有`root`权限

`adm`组：该组内用户可以具有某些`root`权限

其他组，含义暂时未查明。

# 用户管理

# 添加一个用户
设置主目录，用户组，远程`shell`等
登录普通用户
例如，现在要创建用户名为`t1`的用户
```
sudo useradd -s shell -d /home/t1 -m (-g 组名 -G 附加组名1,附加组名2,附加组名3) t1
```

`shell`一般选为`/bin/bash`
-d 后面的目录表示主目录
-m 当设置的主目录事先不存在的时候，该参数可以创建，否则不能创建
-g 添加用户的组名，不选时自动新增一个组，与用户名同名
-G 附加组名，例如`sudo`, `adm`, `cdrom`等附加组

# 修改用户密码
```
sudo passwd t1
```
这样就可以通过su 用户名命令登录t1用户了

# 赋予用户sudo功能
方法1：将用户加入sudo组
```
usermod -G sudo t1
groups t1
```
可以发现，t1用户现在有两个组：t1组和sudo组。登录用户t1即可使用sudo命令。

方法2：在`/etc/sudoers`文件中修改
添加一行代码到`sudoers`文件中
```
t1    ALL=(ALL:ALL) ALL
```

# 修改用户名和主目录
例如要将用户名t1改为t2，然后主目录改为`/home/t2`
要修改主目录，手下手动创建一个目录`/home/t2`。
修改前，先退出t1用户的所有进程(不停exit)，不然无法修改。
```
sudo usermod -d /home/t2 -l t2 t1
```
这样，就把用户名`t1`改为`t2`，主目录改为`/home/t2`

# 删除用户
退出所有`t1`用户的所有进程。
```
sudo userdel -r t1
```
-r表示在删除用户同时，删除主目录和邮件池（报错不影响用户的删除），该参数可以不加，只删除用户。

# 重要文件说明
1. `/etc/passwd`

所有的用户文件，每行一个用户，6个冒号(:)分割成7个字段，每个字段含义分别为

用户名:	用户名

口令:	一般用一个字符代替，但是真实口令存在`/etc/shadow`中

用户id:	`root`用户id为0

组id：	用户所属的用户组。它对应着`/etc/group`文件中的一条记录。

描述：

主目录：

登录shell：

2. `/etc/shadow`文件
用户的真实密码存放处

3. `/etc/group`文件
存放组的信息，第一个字段为组名`G`，最后一个字段为用户名`U`，多个用户用逗号隔开。
注意，仅存储`U`以`G`为附加组的信息。类似`tensory`用户属于`tensory`组的信息不回存储。


# 几个命令
```
groups 用户名	# 查看用户所属组

groups	#查看当前用户所属组

id t1	# 查看用户t1的相关信息
```
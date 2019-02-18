---
title: 配置多台虚拟机之间的免密通信
categories:
- Linux
tags:
- Ubuntu
- SSH
- rsa
toc: true
---
<Excerpt in index | > 
本文讲解多台虚拟机之间的免密登录和免密文件传输<!-- more -->
<The rest of contents | 余下全文>

在搭建Spark+Hadoop平台的时候，通常需要将文件从一个节点批量传递给其他节点，传递的方式是通过scp命令，但是每一次scp都需要对方口令，
才可以传输。为了方便文件传输，需要配置免密登录，这样在使用scp命令传输的时候，可以省去输入密码的繁琐过程。

# 原理
假设有主机名为A、B和C三台虚拟机，IP地址分别为IPA、IPB和IPC。首先每台虚拟机都可以生成属于自己的秘钥（类似于每台虚拟机的口令），
假设生成的秘钥分别为pwdA、pwdB和pwdC，那么只要这些虚拟机相互交换这些秘钥，之后就可以了互相免密通信了。具体的做法是，举个例子，
A将pwdA放到一个文件X中，B和C将各自的秘钥也放到位于A的X中（这个过程中，B和C需要知道A的口令），现在A的X中含有A、B和C三台虚拟机
秘钥，然后由A将文件X分发给B和C，如此，A、B和C均具有另外二者的秘钥了，于是就可以进行免密登录和免密文件传输。

# 准备工作

* 关闭防火墙

在每台虚拟机的终端执行关闭防火墙的命令：
```
sudo ufw disable
```

* 配置IP地址到代号的映射

用IP地址标记某台主机不方便，因此可以为将主机IP地址映射为一个代号，方便命令的输入。例如A、B和C的代号可以分别是master、slave1和
slave2(此处master slave没有特别意义)。在每台虚拟机终端修改/etc/hosts文件
```
sudo vi /etc/hosts
```
在文件末尾追加如下内容：
```
IPA		master
IPB		slave1
IPC		slave2
```

输入完毕，保存。注意，此步骤不是必要的，只是为了稍后秘钥传输和之后文件传输的便捷。

# 生成rsa秘钥
在每台虚拟机终端，生成各自的rsa秘钥。

在任意一个目录新建.ssh目录，我们以在~目录下建立这个目录（这个目录以.开头，ls看不到，ls -a 命令可以看到）：
```
mkdir .ssh
```
进入该目录
```
cd .ssh
```
生成rsa秘钥
```
ssh-keygen -t rsa
```
(此处会询问是否设置密码，连续输入三个回车即可，即默认无密码ssh。如果是大型项目，需要设置密码)
然后会发现，在.ssh目录下，多了两个文件，分别名为  id_rsa和id_rsa.pub，分别为私钥和公钥，虚拟机之间需要交换的公钥。

# 收集秘钥

现在，每台虚拟机都有了各自的秘钥，现在指定某台虚拟机，将公钥收集在一起。此处指定master，即虚拟机A。
在A的终端的.ssh目录下：
```
cat id_rsa.pub >> authorized_keys
```
即将A的公钥放到authorized_keys文件中。

在B的终端的.ssh目录下：
```
scp id_rsa.pub | ssh 主机名@master 'cat >> ~/.ssh/authorized_keys'
```
（此处要输入A的口令）
即将B的公钥放到A的authorized_keys中

在C的终端的.ssh目录下：
```
# scp id_rsa.pub | ssh 主机名@master 'cat >> ~/.ssh/authorized_keys'
ssh master主机名@master 'cat id_rsa.pub >> ~/.ssh/authorized_keys'
```
（此处要输入A的口令）
即将C的公钥放到A的authorized_keys中

# 分发秘钥
现在A的authorized_keys中有了A、B和C的秘钥了，只需要将这个文件分发给B和C即可。
在A的终端分别执行：

```
scp authorized_keys | ssh B@slave1:~/.ssh/
```
（此处要输入B的口令）

```
scp authorized_keys | ssh C@slave2:~/.ssh/
```
（此处要输入C的口令）


# 完成配置
现在A、B和C分别具有另外两者的秘钥了，可以使用 ssh 命令免密登录或scp免密传输文件，命令如下：

* 在A的终端登录到B主机：
```
ssh slave1
```
（不用口令就可以登录到B的终端）

* 在C的终端传文件给A主机：
```
scp file_dir/file_name | ssh A@master:/some_dir/
```
（不用口令）

# 注意
* 使用scp命令的时候，区分主机名和IP地址的代号。
* 这里master和slave没有实际意义，任意一台虚拟机都可完成收集/分发秘钥工作。

# 参考
* [虚拟机中多个Linux系统之间配置免秘钥登录](https://blog.csdn.net/diyxiaoshitou/article/details/52471097)
* [id_rsa id_pub 公钥与私钥](https://blog.csdn.net/diyxiaoshitou/article/details/52471097)
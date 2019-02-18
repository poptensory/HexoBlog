---
title: 将本地项目添加到Git版本管理并关联Github仓库
categories:
- 软件工程
tags:
- Git
- Github
- 版本管理
- 软件工程
toc: true
---
<Excerpt in index | > 
介绍Git的用法，以及关联Github仓库<!-- more -->
<The rest of contents | 余下全文>

# 简介
Git是一个版本管理软件，初始版本由Linus Torvalds编写，其最大的特点之一是[分布式]，不用联网，可以实现本地版本管理。
Github是一个代码托管平台，由Preston-Werner创办。
Git和Github又一次说明，世界上名字类似两个东西，可能没有任何关系（Java和JavaScript，Angular和Angular2，雷锋和雷峰塔......以及老婆和老婆饼）。

# Git实现版本管理
1. Git bash安装过程略
2. 打开Git终端窗口，建立文件夹GitTest
```
mkdir GitTest
```
3. 进入该文件夹，创建一些文件file1.txt, file2.txt
```
cd GitTest
vi file1.txt
vi file2.txt
```
4. 通过以下命令，对该文件夹（下的所有文件）进行版本管理初始化
```
git init
```
5. add操作
```
git add .
```
. 表示当前目录下的文件和目录
6. 查看状态
```
git status
```
7. 提交修改
```
git commit -m 'first commit'
```
8. 查看状态
```
git status
```

# 将本地项目上传到Github
1. 在Github上创建一个代码仓库，复制仓库地址(https://github.com/username/projectName.git)

2. 将本地项目(上一步创建的Test文件夹)关联到远程仓库
进入GitTest文件夹
```
cd GitTest
git remote add origin https://github.com/username/projectName.git
```

3. 项目推送到远程仓库
```
git push -u origin master
```
关联后的第一次推送需要加上参数-u，后面不用。

4. 去github页面查看项目是否push成功

# 参考
1. https://blog.csdn.net/zamamiro/article/details/70172900
2. https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/001373962845513aefd77a99f4145f0a2c7a7ca057e7570000
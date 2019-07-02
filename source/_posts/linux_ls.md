---
title: Linux的硬链接与软链接
categories:
- Linux
tags:
- inode
- 硬链接
- 软链接
toc: true
---
<Excerpt in index | > 
介绍Linux的硬链接和软链接<!-- more -->
<The rest of contents | 余下全文>


# Linux的inode
我们在使用`ls -al -i`命令查看目录的文件和目录，如下图

每行的第一列数据，就是对应的inode。数据在磁盘上以块(block)存储，系统读取文件的过程为：通过文件名找到inode，进而找到inode的信息，即数据所在的块，最后读取数据。
因此inode可以简单理解为：数据在磁盘上的位置索引。inode大小一般为128字节或256字节，7或8位。

# 硬链接与软链接
二者的概念如下图所示：

可以看到，硬链接是直接指向了文件所在块，软链接指向了已存在的一个文件名。类似于Java中的引用。

硬链接的文件类型可以是很多文件的类型，而软链接的文件类型就只是[软链接]这种类型，类型代号为[字母l]。

# 命令与实验
1.链接的创建 

2.硬链接的个数
`ls -ali`命令查看文件的长格式信息，下图中的数字表示，硬链接的个数。

![图的说明](https://raw.githubusercontent.com/poptensory/MarkdownPictures/master/v2-9c3af05bf2770df15047b331ec8b8226_r.jpg "可选标题")
![Alt text](https://raw.githubusercontent.com/poptensory/MarkdownPictures/master/v2-c36bc7122703a7927de21b1b6522bf3d_r.jpg "Optional title")


| 对比点 | Hive | Hbase | 分析 |
| :------:| :--------: | :------: | :------: |
| 对入库数据的要求 | 结构化（无需主键） | 结构化或非结构化，但是必须存在主键 | Hive要求数据结构化，可以没有主键。Hbase要求必须存在主键。|
| 800w+数据入库 | <font color=#FF0000>2.219s</font> | 约5min | Hive数据入库的过程就是将文件上传至HDFS并建立对应关系的过程，因此很快 |
| 统计表中记录数 | <font color=#FF0000>30.56s</font> | 527.46s | Hive：将SQL语句翻译成MR过程，扫描所有记录。Hbase：数主键的个数，扫描的k-v对的数目是最多为 列数*记录数|
| 根据主键查询 | 0.182s | <font color=#FF0000>0.0148s</font> | Hive：依然是扫描表中记录，线性查找，平均时间复杂度O(n)。Hbase：根据key查找value，时间复杂度O(1)|
| 操作的语言 | <font color=#FF0000>HQL</font> | 新的语言 | Hive的语言为类似SQL的HQL。Hbase的语言是一种新的语言，学习成本更高。	|
| CRUD操作 | 查询 | <font color=#FF0000>插叙和更新</font> | Hive的数据存在HDFS，因此只能查询而无法修改。Hbase：数据可以查询和更新。 | 


<div style="text-align:center">
<table style="margin:auto" border="1">
   <tr>
      <td rowspan="2">主键</td>
      <td colspan="2">列簇1</td>
      <td>列簇2</td>
      <td colspan="3">列簇3</td>
   </tr>
   <tr>
      <td>列11</td>
      <td>列12</td>
      <td>列21</td>
      <td>列31</td>
      <td>列32</td>
      <td>列33</td>
   </tr>
   <tr>
      <td>1</td>
      <td>#</td>
      <td>1</td>
      <td>#</td>
      <td>3</td>
      <td>#</td>
      <td>#</td>
   </tr>

</table>
</div>
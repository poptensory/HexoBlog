---
title: Hive和Hbase对比
categories:
- 大数据
tags:
- Hive
- Hbase
- 数据仓库
- 数据库
toc: true
---
<Excerpt in index | > 
对比Hive和Hbase。<!-- more -->
<The rest of contents | 余下全文>
Hive和Hbase都是基于Hadoop的HDFS对数据进行管理（我没有说"存储"）。

Hive是对MapReduce的封装，Hbase是对HDFS的封装。

# 文件存储的区别
将本地文本文件导入Hive的时候，该文件会被存在在HDFS，因此可以说Hive是基于HDFS对数据进行存储的。
Hive只提供了一套访问HDFS的API，这套API对用户而言类似于SQL，用户输入的HQL被翻译成MR过程对数据进行访问。
因此，也可以说Hive没有数据存储功能，既然如此，可以想象，将数据导入Hive的时候，效率是非常高的，因为Hive根本就没有存储数据，只是将数据放在了HDFS上。而已。

Hbase是真正的数据库，而且区别于SQLServer, MySQL和MongoDB等关系数据库，Hbase是一种key/value数据库（redis??）。
想到key/value，就想到了字典，字典的最大特点就是：在已知key的条件下，O(1)时间内访问value。因此Hbase的查询速度很快。
将数据导入Hbase，首先要将数据放在HDFS上，再将HDFS上的数据导入Hbase表，是真正的导入，因此在数据导入上，效率比Hive差很多（不过没关系，原因后面解释）。
Hbase允许存储非结构化数据，因为key/value存储的方式，在Hbase表中，允许“表中有表”(column family：列簇的概念)，而这违反了关系型数据库的第一范式，所以它不是关系型数据库。

例如，Hbase存储的结构如下表，其中\#表示该行没有这个字段。一个列簇可能有多个列。

<div style="text-align:center"><table style="margin:auto" border="1">   <tr>      <td rowspan="2">主键</td>      <td colspan="2">列簇1</td>      <td>列簇2</td>      <td colspan="3">列簇3</td>   </tr>   <tr>      <td>列11</td>      <td>列12</td>      <td>列21</td>      <td>列31</td>      <td>列32</td>      <td>列33</td>   </tr>   <tr>      <td>1</td>      <td>#</td>      <td>1</td>      <td>#</td>      <td>3</td>      <td>#</td>      <td>#</td>   </tr>   <tr>      <td>2</td>      <td>#</td>      <td>#</td>      <td>2</td>      <td>#</td>      <td>#</td>      <td>#</td>   </tr>   <tr>      <td>3</td>      <td>32</td>      <td>#</td>      <td>#</td>      <td>3</td>      <td>#</td>      <td>#</td>   </tr>   <tr>      <td>4</td>      <td>#</td>      <td>9</td>      <td>4</td>      <td>#</td>      <td>3</td>      <td>7</td>   </tr></table></div>


# 实用场景及原因分析
Hive适合大规模离线数据的批量分析。
首先，大规模数据采用Hive无可厚非，因为Hive基于分布式存储系统。
其次，为什么是离线数据？因为Hive不提供数据更新的接口，所以在线实时数据基本上不能用Hive来管理，Hive是数据仓库。
又为什么是批量分析？批量分析指的是，例如分组查询统计，根据某个字段条件查询等，这个关系型数据可以做到，但是Hive是分布式，因此肯定比传统的单机数据库效率高。

Hbase适合在线实时数据存储。
Hbase提供了数据更新接口。废话，不能更新数据好意思叫数据库？人家那叫数据仓库。因此Hbase适合用于实时在线数据存储，且数据的访问是根据key来访问的，效率非常高。

# 实验对比

| 对比点 | Hive | Hbase | 分析 |
| :------:| :--------: | :------: | :------: |
| 对入库数据的要求 | 结构化（无需主键） | 结构化或非结构化，但是必须存在主键 | Hive要求数据结构化，可以没有主键。Hbase要求必须存在主键。|
| 800w+数据入库 | <font color=#FF0000>2.219s</font> | 约5min | Hive数据入库的过程就是将文件上传至HDFS并建立对应关系的过程，因此很快 |
| 统计表中记录数 | <font color=#FF0000>30.56s</font> | 527.46s | Hive：将SQL语句翻译成MR过程，扫描所有记录。Hbase：数主键的个数，扫描的k-v对的数目是最多为 列数*记录数|
| 根据主键查询 | 0.182s | <font color=#FF0000>0.0148s</font> | Hive：依然是扫描表中记录，线性查找，平均时间复杂度O(n)。Hbase：根据key查找value，时间复杂度O(1)|
| 操作的语言 | <font color=#FF0000>HQL</font> | 新的语言 | Hive的语言为类似SQL的HQL。Hbase的语言是一种新的语言，学习成本更高。	|
| CRUD操作 | 查询 | <font color=#FF0000>插入和更新</font> | Hive的数据存在HDFS，因此只能查询而无法修改。Hbase：数据可以查询和更新。 | 
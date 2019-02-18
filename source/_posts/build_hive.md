---
title: Hive搭建(集成mysql)
categories:
- 大数据
tags:
- Hive
- Hadoop
- Linux
toc: true
---
<Excerpt in index | > 
本文详细讲述Hive的搭建过程。<!-- more -->
<The rest of contents | 余下全文>
Hive是一种数据仓库，建立在HDFS之上，因此，搭建Hive之前需要成功搭建Hadoop平台。

# 准备工作
**1.搭好Hadoop平台。**
**2.准备好Hive安装包和mysql驱动**
Hive安装包可以到官网下载，mysql驱动官网貌似找不到*.jar包，可以[点我下载mysql驱动](https://github.com/poptensory/OnlineResource/blob/master/mysql-connector-java-5.1.41-bin.jar?raw=true)。
**3.注意:如果是集群Hive只需要安装在一个节点上**

# Hive的配置
**1.解压Hive安装包**
将Hive安装包和mysql驱动放在用户主目录（或其他目录，本文以用户主目录为例）。

通过如下命令解压Hive安装包：
```
tar -zxvf apache-hive-2.3.3-bin.tar.gz
```
![tar hive](https://raw.githubusercontent.com/poptensory/OnlineResource/master/1.png)

解压后会出现apache-hive-2.3.3-bin文件，此处更名为更简单的名字，命令如下：
```
mv apache-hive-2.3.3-bin hive-2.3.3
```

现在hive-2.3.3这个目录就是Hive主目录。

**2.配置Hive环境变量**
打开环境变量配置文件，命令如下：
```
sudo vi /etc/profile
```
在文件末尾添加如下内容：

```
# hive home dir
export HIVE_HOME=/home/tensory/hive-2.3.3
export PATH=$PATH:$HIVE_HOME/bin
```
添加完毕之后，保存，输入如下命令激活环境变量：
```
source /etc/profile
```

激活之后，输入如下命令检测环境变量是否配置好：
```
hive –version
```

顺利的话，会看到类似下图的Hive版本等信息。
![hive version](https://raw.githubusercontent.com/poptensory/OnlineResource/master/4.png)

**3.修改Hive配置文件hive-site.xml**
进入Hive主目录，进入其下的conf目录。输入如下命令，从模板复制一份配置文件：
```
cp hive-default.xml.template hive-site.xml
```

修改hive-site.xml，先删除<configuration>标签里面的所有内容（为什么是所有？）
放入下面这些代码：
```xml
<property>
    <name>javax.jdo.option.ConnectionUserName</name>
    <value>tensory</value>
</property>
<property>
    <name>javax.jdo.option.ConnectionPassword</name>
    <value>123456</value>
</property>
<property>
    <name>javax.jdo.option.ConnectionURL</name>
    <value>jdbc:mysql://192.168.174.135:3306/hive?useSSL=false</value>
</property>
<property>
    <name>javax.jdo.option.ConnectionDriverName</name>
    <value>com.mysql.jdbc.Driver</value>
</property>
```
修改后的hive-site.xml文件就像这样子：
![hive-site](https://raw.githubusercontent.com/poptensory/OnlineResource/master/7.png)

上图的"tensory"和"123456"为稍后mysql连接hive的账密，IP地址改为hive所在节点的IP地址。其他不用修改，影响不大。保存。


**4.修改Hive配置文件hive-env.sh**
先从模板复制一份，然后再修改，命令如下：
```
cp hive-env.sh.template hive-env.sh
```

通过vi打开hive-env.sh，在里面添加HADOOP的主路径，类似下面这样：
```
HADOOP_HOME=/home/tensory/hadoop-2.6.5/
```

保存。

# mysql的安装与集成

**1.安装mysql相关服务**
输入mysql安装命令:
```
sudo apt-get install mysql
```

![mysql install](https://raw.githubusercontent.com/poptensory/OnlineResource/master/10.png)

稍等会询问，输入y。
输入y，等待一段时间后，会提示输入MySQL的root用户的密码。这个密码，稍后用于mysql的登录。设置即可。

安装完毕之后，默认mysql是启动的。

**2.用root用户登录mysql**
输入登录命令:
```
mysql -u root -p
```
会提示输入密码，就是刚刚设置的密码。然后，就进入了mysql的命令模式，如下图：
![mysql>](https://raw.githubusercontent.com/poptensory/OnlineResource/master/12.png)

**3.创建一个hive数据库**
在mysql的命令模式下，创建hive数据库，命令为：
```
create database hive;
```

然后，再通过如下三行命令，集成mysql到hive：
```
CREATE USER  'tensory'@'%'  IDENTIFIED BY '123456';

GRANT ALL PRIVILEGES ON  *.* TO 'tensory'@'%' WITH GRANT OPTION;

flush privileges;
```

如下图：
![create hive](https://raw.githubusercontent.com/poptensory/OnlineResource/master/13.png)

其中'tensory'和'123456'为在hive-site.xml中设置的账密，用于hive连接mysql。

通过exit;命令退出mysql交互式模式。

**4.修改mysql的配置文件**
进入/etc/mysql/mysql.conf.d/mysqld.cnf里面，代码为：
```
sudo vi /etc/mysql/mysql.conf.d/mysqld.cnf
```

找到bind-address这一行，注释掉就可以，如下图，注释掉：
![bind address](https://raw.githubusercontent.com/poptensory/OnlineResource/master/14.png)

**5.拷贝mysql驱动程序**
进入主目录，将mysql驱动程序拷贝到hive-2.3.3/lib/目录下：
```
cp mysql-connector-java-5.1.41-bin.jar hive-2.3.3/lib/
```

# 初始化元数据
**1.启动Hadoop**
在任何一个目录输入命令：
```
start-all.sh
```
可以通过jps命令测试是否启动hadoop。

**2.初始化**
初始化命令为：
```
schematool -dbType mysql -initSchema
```

可能遇到下面这个问题：
![cannot connect](https://raw.githubusercontent.com/poptensory/OnlineResource/master/15.png)


重启msyql服务即可，命令为：
```
sudo service mysql restart
```
重启之后，再输入初始化命令，顺利的话，可以看到类似如下图的成功信息：
![success](https://raw.githubusercontent.com/poptensory/OnlineResource/master/16.png)

# 测试
**1.简单测试**
  s1:输入hive，进入hive交互模式。
  s2:查看有哪些数据库: 
  ```
  show databases;
  ```
  s3:创建一个数据库：
  ```
  create database dbTest;
  ```
  s4:再查看数据库：
  ```
  show databases;
  ```
  类似下图：
  ![test](https://raw.githubusercontent.com/poptensory/OnlineResource/master/17.png)

**2.建表**

依然在hive交互模式下，通过如下命令进入dbTest数据库下，开始建表
```
use dbTest
```

建立一个名为tbTest的表，建表命令：
```
create table tbTest (id string, name string, age string);
```

**3.创建数据**
（退出hive交互模式）创建一个文本文件，tbtest.txt，写入如下内容(或者其他内容)：
```
111,Mike,21
222,Bob,20
333,Cindy,22
```

**4.导入数据**
（进入hive交互模式）向表tbTest中导入上面的数据，命令为：
```
load data local inpath '/home/tensory/tbtest.txt' into table tbtest;
```

注意选择创建的tbtest.txt的路径。

**5.查看数据**
查看表中的所有数据，命令为：
```
select * from tbtest;
```

结果如下图：
![select result](https://raw.githubusercontent.com/poptensory/OnlineResource/master/19.png)

发现后面两列为NULL，因为hive建表的时候默认的分隔符不是文本文件中的分隔符：逗号，输入如下命令修改分隔符：
```
alter table tbtest set SERDEPROPERTIES('field.delim'=',');
```

再次查看，正常，如下图：
![select result](https://raw.githubusercontent.com/poptensory/OnlineResource/master/20.png)

**6.查看HDFS**
进入hdfs的web界面，查看文件系统，可以看到对应的表，和对应的数据文件，如下图:
![hdfs](https://raw.githubusercontent.com/poptensory/OnlineResource/master/21.png)

把这个文件下载下来，查看，会发现和导入的那个文件内容完全相同。

# 简单的对比

为了对比性能，我写了一个Java封装SQL向MySQL写入数据的Demo。Java+SQL+MySQL写入30000条记录用时20分钟左右，
Hive导入8000000（800w）条相同格式的记录，用时14秒左右。写入数据库的效率相差几个数量级，而且Hive导入数据的代码只有一句，
Java需要读取*.txt、分割字符串、向表写入记录，代码繁琐。

至此，Hive配置、mysql集成、Hive测试完成。

# 参考
[Hive安装与配置](https://www.cnblogs.com/kinginme/p/7233315.html)
[Hive集成mysql数据库](http://www.cnblogs.com/kinginme/p/7249533.html)
[Hive安装与配置详解](https://www.cnblogs.com/dxxblog/p/8193967.html)
---
title: Scala编写Spark程序Windows本地调试、打jar包、提交远程集群运行
categories:
- 大数据
tags:
- Scala
- Spark
- debug
toc: true
---
<Excerpt in index | > 
本文详述Scala本地debug、打jar包、提交到远程集群运行。<!-- more -->
<The rest of contents | 余下全文>


# 开发环境

编译器：InteliJ IDEA
JD版本：1.8
Spark版本：2.2.2
Hadoop版本：2.7.3
Scala版本：2.11.8

注意：
1. IDEA自带Mave,sbt等工程管理工具，非常方便，所以选择IDEA。
2. Windows本地调试，需要JDk，Spark，Hadoop，Scala，版本应当与集群版本保持一致。Spark和Hadoop的版本，只要不相差太大就没问题，但是本地和集群的JDK、Scala必须最好对应保持版本一致。

# 本地调试纯Scala程序

1. 先下载JDK，Spark，Hadoop的安装包，解压到某个位置。

2. 配置环境变量
配置JAVA_HOME, SPARK_HOME, HADOOP_HOME
在PATH这个变量中，添加JDK,Spark, Hadoop的bin目录对应的目录，如下图所示：
![Env](https://raw.githubusercontent.com/poptensory/OnlineResource/master/FigIDEAScala/IDEA_Scala_env_var.png "Env")

测试是否配置好环境变量，操作及正确反馈如下图：
JDK:
![Java](https://raw.githubusercontent.com/poptensory/OnlineResource/master/FigIDEAScala/IDEA_Scala_java-cmd.png "Java")

Spark:
![Spark](https://raw.githubusercontent.com/poptensory/OnlineResource/master/FigIDEAScala/IDEA_Scala_spark-cmd.png "Spark")

Hadoop:
![Hadoop](https://raw.githubusercontent.com/poptensory/OnlineResource/master/FigIDEAScala/IDEA_Scala_hadoop-cmd.png "Hadoop")

3. IDEA中建立Scala项目
step1: 建立一个项目
Scala>IDEA>Next，建立Scala项目。
![Scala](https://raw.githubusercontent.com/poptensory/OnlineResource/master/FigIDEAScala/IDEA_Scala_1.png "Scala")
step2: 配置
特定注意配置好JDK路径，Scala路径和版本问题
![Scala](https://raw.githubusercontent.com/poptensory/OnlineResource/master/FigIDEAScala/IDEA_Scala_2.png "Scala")
选择Scala源文件(含有bin等目录的Scala文件)。
![Scala](https://raw.githubusercontent.com/poptensory/OnlineResource/master/FigIDEAScala/IDEA_Scala_3.png "Scala")
![Scala](https://raw.githubusercontent.com/poptensory/OnlineResource/master/FigIDEAScala/IDEA_Scala_4.png "Scala")
建立目录，设置Sources源文件目录。
![Scala](https://raw.githubusercontent.com/poptensory/OnlineResource/master/FigIDEAScala/IDEA_Scala_5.png "Scala")
建立一个Scala类。
![Scala](https://raw.githubusercontent.com/poptensory/OnlineResource/master/FigIDEAScala/IDEA_Scala_6.png "Scala")
![Scala](https://raw.githubusercontent.com/poptensory/OnlineResource/master/FigIDEAScala/IDEA_Scala_7.png "Scala")
编写对应代码，并运行。
可以看出，可以代码通过。
![Scala](https://raw.githubusercontent.com/poptensory/OnlineResource/master/FigIDEAScala/IDEA_Scala_8.png "Scala")

上面的是纯粹的Scala程序，不需要Hadoop或者Spark也可以正常运行。

# 本地调试Scala Spark程序
下面我们将代码编写更复杂一点。
step1: 导入Spark的jar包
![Scala](https://raw.githubusercontent.com/poptensory/OnlineResource/master/FigIDEAScala/IDEA_Scala_9.png "Scala")
step2: 选择$SPARK_HOME/jars中的所有包。
![Scala](https://raw.githubusercontent.com/poptensory/OnlineResource/master/FigIDEAScala/IDEA_Scala_10.png "Scala")
Apply > OK
![Scala](https://raw.githubusercontent.com/poptensory/OnlineResource/master/FigIDEAScala/IDEA_Scala_11.png "Scala")
编写带有SparkContext的Scala程序，创建sc。运行。可以看到，能够正常运行Spark程序了。
![Scala](https://raw.githubusercontent.com/poptensory/OnlineResource/master/FigIDEAScala/IDEA_Scala_12.png "Scala")


# 打包与提交到远程集群
1. 打包
步骤如下：
![Scala](https://raw.githubusercontent.com/poptensory/OnlineResource/master/FigIDEAScala/IDEA_Scala_13.png "Scala")
![Scala](https://raw.githubusercontent.com/poptensory/OnlineResource/master/FigIDEAScala/IDEA_Scala_14.png "Scala")
![Scala](https://raw.githubusercontent.com/poptensory/OnlineResource/master/FigIDEAScala/IDEA_Scala_15.png "Scala")
![Scala](https://raw.githubusercontent.com/poptensory/OnlineResource/master/FigIDEAScala/IDEA_Scala_16.png "Scala")
如下图，可以看到out/artifacts/xxx/目录下，多了一个jar包：Scala2JAR.jar
![Scala](https://raw.githubusercontent.com/poptensory/OnlineResource/master/FigIDEAScala/IDEA_Scala_17.png "Scala")
2.提交运行
得到这个jar包之后，利用WinSCP等工具，将文件上传到集群上。然后输入如下命令运行。
```
spark-submit --master spark://master_ip:7077 --class "类名" jar包.jar
```
![Scala](https://raw.githubusercontent.com/poptensory/OnlineResource/master/FigIDEAScala/IDEA_Scala_18.png "Scala")
出现如上的INFO-Level的日志，不报错，就说明成功运行。

# 可能出现的Bug

如果出现如下错误：最可能的原因是，本地和集群的Spark或Scala版本不一致造成的。
![Scala](https://raw.githubusercontent.com/poptensory/OnlineResource/master/FigIDEAScala/IDEA_Scala_bug.png "Scala")

# 小结

将近一个月没写博客了，以后多挤点时间系统学习一些技能，知其所以然，争取坚持每周至少一篇总结的博客。加油！

累了可以听听左上角的相声。
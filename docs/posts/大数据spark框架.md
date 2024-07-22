---
title: 大数据hadoop生态圈中的spark框架
date: 2023-07-06 08:48:43
tags:
---

# Spark框架

在学习Spark框架之前我们需要弄清楚什么是Spark?

**什么是Spark？**

在引入Spark之前我们需要提出`hadoop系统生态圈`，Hadoop是一个开源的分布式计算和存储框架，它可以处理海量数据，具有可靠、高效、可伸缩的特点。Hadoop主要包括两个组件：HDFS（Hadoop Distributed File System）和MapReduce。HDFS是一个分布式文件系统，负责数据的存储。MapReduce是一个分布式计算框架，负责数据的处理。

而我们的`Spark的引入`就是为了替代`MapReduce`

`spark`与`MapReduce`对比

当谈到大规模数据处理时，MapReduce和Spark都是非常流行的框架。以下是它们之间的一些主要区别：

1. 处理模型：

MapReduce是一个批处理模型，它适用于离线数据处理，需要将数据存储到磁盘中并进行多次读写。而Spark是一个基于内存的计算框架，它支持交互式和流式数据处理。

2. 处理速度：

由于Spark是基于内存的计算框架，它可以比MapReduce更快地处理数据。这是因为它可以利用内存中的数据进行计算，而不需要像MapReduce一样频繁地进行读写操作。

3. 内存管理：

MapReduce没有内存管理系统，数据必须从磁盘读取，并且在处理完后必须写回磁盘。而Spark有一个内存管理系统，可以将数据存储在内存中以提高性能。

4. 编程模型：

MapReduce使用Java编程语言和可扩展标记语言（XML）来编写作业。而Spark支持Java、Scala、Python等多种编程语言，并且提供了更加高级的API和函数式编程模型。

5. 支持功能：

Spark支持比MapReduce更多的功能，如内置的`机器学习库`、图形处理等。

| 特征     | MapReduce                                 | Spark                                                        |
| -------- | ----------------------------------------- | ------------------------------------------------------------ |
| 处理模型 | 批处理模型，适用于离线数据处理            | 基于内存的计算框架，支持交互式和流式数据处理                 |
| 处理速度 | 相对较慢，需要频繁进行读写操作            | 较快，可以利用内存中的数据进行计算                           |
| 内存管理 | 没有内存管理系统，数据必须从磁盘读取/写回 | 有内存管理系统，可以将数据存储在内存中以提高性能             |
| 编程模型 | 使用Java编程语言和XML                     | 支持Java、Scala、Python等多种编程语言，并提供更高级的API和函数式编程模型 |
| 支持功能 | 功能相对较少                              | 支持内置机器学习库、图形处理等功能                           |

请注意，这只是Spark和MapReduce之间的一些主要区别，实际上它们之间还有其他的区别和特征。此表仅供参考。

总的来说，Spark相对于MapReduce具有更好的性能、更多的功能和更灵活的编程模型，在处理大规模数据时更加适用。但是，对于一些离线数据处理的任务，MapReduce仍然是一个可行的选择。

![](https://124newblog-1309411887.cos.ap-nanjing.myqcloud.com/images/202307060910824.jpg)

**为什么Hadoop生态圈有两个数据库？**

从上图我们可以看出来这里至少有两个数据库，一个是实时分布式数据库，另外一个是数据仓库。

其实这两个数据库是有一些区别的

1. 数据处理模型

数据仓库通常使用**批处理模型**，将数据从源系统中定期抽取并加载到数据仓库中，然后进行批量处理和分析。而实时分布式数据库使用**流处理**模型，能够**实时**地接收和处理数据流。

2. 数据存储方式

数据仓库通常采用**关系型数据库**，如`mysql`等，或者Hadoop生态圈中的`Hive`等，以支持复杂的查询和分析。而实时分布式数据库通常采用**NoSQL数据库**，如`MongoDB`等，以支持高速写入和实时查询。

3. 数据处理速度

数据仓库通常需要进行多次的数据转换和数据迁移，这可能会导致较长的处理时间和延迟。而实时分布式数据库能够实时地处理和分析数据，提供快速的响应和实时决策支持。

4. 数据访问模式

数据仓库通常用于支持复杂的查询和分析，例如OLAP（联机分析处理），其数据访问模式通常是基于**离线**数据的批处理。而实时分布式数据库通常用于支持**实时**查询和交互式分析，其数据访问模式通常是基于实时流数据的流处理。

总的来说，数据仓库和实时分布式数据库都是用于存储和处理大规模数据的系统，但它们的数据处理模型、数据存储方式、数据处理速度和数据访问模式等方面存在一些差异。选择适合自己业务需求的数据存储和处理系统，应根据具体情况进行综合考虑。

**Spark组成**

组成的模块有

- Spark Core
- 基于Spark Core下还有 Spark Sql、 Spark steaming

Spark Core 是直接处理分布式集群中进行数据处理和分析的。

Spark Sql 是对数据库进行的数据处理与查询分析

Spark Steaming是处理流计算的

**批处理与流处理之间的区别**

*处理方式*

批处理是将一定时间内的数据收集到一起，形成一个批次后再进行处理和分析，通常是离线处理方式。而流处理是对流数据进行实时处理，数据一旦到达就立即进行处理，通常是在线处理方式。

*特点*

批处理通常具有较高的稳定性和可靠性，可以处理大量的数据，但响应时间较长。而流处理具有较高的实时性和低延迟，但可能会因为数据流量过大而导致性能问题。

# Sparkcore（所有涉及到scala语法的过程嵌入到当中的代码注释当中)

## 常用语法部分

- val 表示不可变的数据值
- var 表示可变的数据值

**例子**

如何将数组内的每一个数都加上‘1’？

可以使用函数

```scala
val rdd = sc.parallelize(1 to 10)
val mapRdd = rdd.map(addOne)

def addOne(x:Int):Int={
    x + 1
  }
```

map是进一返一的操作

可以使用隐函数

```scala
val rdd = sc.parallelize(1 to 10)
```



## 高阶函数

高阶函数也就是说一个函数的参数部分包含了函数

## 对RDD的操作

对`RDD`的操作也就是是一个对数据分区底层的操作，是直接接触到数据库内的操作。(`SparkCore`)

作为区别来说`SparkSql`，这就是在数据库外对数据库内的操作。

一个函数只要返回时一个`RDD`那么这个就是一个`transfer`操作

否则为`action`操作

**filter**

```scala
package edu.qdu.spark.transform

import org.apache.spark.{SparkConf, SparkContext}

object Transform_Filter {
  def main(args: Array[String]): Unit = {
    val conf = new SparkConf().setAppName("filter").setMaster("local[*]")
    val sc = new SparkContext(conf)

    val rdd = sc.parallelize(List(
      Student(1,"aa","man",22),
      Student(2,"bb","female",25),
      Student(3,"cc","man",21),
      Student(4,"dd","female",20),
    ))
    val filteredRdd = rdd.filter(filterFemale)
    filteredRdd.foreach(println)
    sc.stop()
  }

  def filterFemale(s:Student)={
    s.gender.equals("female")
  }

  def filterEven(num:Int)={
    num%2==0
  }
}


case class Student(val sid:Int,val name: String,val gender:String,val age:Int)
```

**flatmap**

```scala
package edu.qdu.spark.transform

import org.apache.spark.{SparkConf, SparkContext}

object TransForm_FlatMap {
  def main(args: Array[String]): Unit = {
    val conf = new SparkConf().setAppName("flat").setMaster("local[*]")
    val sc = new SparkContext(conf)
    val rdd = sc.parallelize(List(
      Array(1,2,3),
      Array(4,5,6),
      Array(7,8,9),
    ))
    val flatRdd = rdd.flatMap(a=>a.map(x=>x))
    println("flatRdd")
    flatRdd.foreach(println)
    
    sc.stop()
  }
}

```

**union**

```scala
package edu.qdu.spark.transform

import org.apache.spark.{SparkConf, SparkContext}

object Transform_Union {
  def main(args: Array[String]): Unit = {
    val conf = new SparkConf().setAppName("union").setMaster("local[2]")
    val sc = new SparkContext(conf)
    val rdd1 = sc.parallelize(1 to 3)
    val rdd2 = sc.parallelize(1 to 3)
    val rdd = rdd1.union(rdd2)
    rdd.foreach(println)

    sc.stop()
  }
  def addOne(num:Int):Int ={
    num + 1
  }
}

```

```text
union operation
1
2
3
1
2
3
intersection operation
1
2
3
```

可以发现union可以合并两个集合，并不会对原集合操作做任何的改变，即不去重。

intersection 交集

**groupByKey**

根据key进行分组

```scala
package edu.qdu.spark.transform

import org.apache.spark.{SparkConf, SparkContext}

object Transform_GroupByKey {
  def main(args: Array[String]): Unit = {
    val conf = new SparkConf().setAppName("group").setMaster("local[2]")
    val sc = new SparkContext(conf)
    val rdd = sc.parallelize(List(("a",1),("b",1),("c",1),("a",1)))
    val groupRdd = rdd.groupByKey()
    groupRdd.foreach(println)

    sc.stop()
  }
}

```

```text
(a,CompactBuffer(1, 1))
(b,CompactBuffer(1))
(c,CompactBuffer(1))
```

如果将`("a",1)`改为`(a,2)`

```text
val rdd = sc.parallelize(List(("a",1),("b",1),("c",1),("a",2)))
```

```text
(a,CompactBuffer(1, 2))
(b,CompactBuffer(1))
(c,CompactBuffer(1))
```

**reduceByKey**

对相同key的value值进行聚合（聚合操作包括了取max，min等操作）

```scala
package edu.qdu.spark.transform

import org.apache.spark.{SparkConf, SparkContext}

object Transform_reduceByKey {
  def main(args: Array[String]): Unit = {
    val conf = new SparkConf().setAppName("group").setMaster("local[2]")
    val sc = new SparkContext(conf)
    val rdd = sc.parallelize(List(("a",1),("b",1),("c",1),("a",2),("b",3)))
    val reduceRdd = rdd.reduceByKey((x,y)=>if (x>=y) x else y )
    reduceRdd.foreach(println)

    sc.stop()
  }
}

```

```text
(a,2)
(b,3)
(c,1)
```

**sortByKey**

所有根据key进行排序（注意只要根据key进行排序就需要元组的数据结构类型）

默认是升序

```scala
package edu.qdu.spark.transform

import org.apache.spark.{SparkConf, SparkContext}

object Transform_sortByKey {
  def main(args: Array[String]): Unit = {
    val conf = new SparkConf().setAppName("sort").setMaster("local[2]")
    val sc = new SparkContext(conf)
    val rdd = sc.parallelize(List(("a",1),("b",1),("c",1),("a",2),("b",3),("b",2)))
    val sortRdd = rdd.sortByKey()

    sortRdd.foreach(println)

    sc.stop()
  }
}

```

```text
(c,1)
(a,1)
(a,2)
(b,1)
(b,3)
(b,2)
```

**Cartesain**笛卡尔积操作

也就是两两进行**组合**的方式

```scala
package edu.qdu.spark.transform

import org.apache.spark.{SparkConf, SparkContext}

object Transform_Catesain {
  def main(args: Array[String]): Unit = {
    val conf = new SparkConf().setAppName("Cartesain").setMaster("local[2]")
    val sc = new SparkContext(conf)
    val rdd1 = sc.parallelize(List('a','b','c'))
    val rdd2 = sc.parallelize(1 to 3)
    val rdd = rdd1.cartesian(rdd2)

    rdd.foreach(println)

    sc.stop()
  }
}

```

```text
(a,2)
(a,1)
(a,3)
(b,1)
(c,1)
(b,2)
(b,3)
(c,2)
(c,3)

```

**Repartition**重新分配rdd分区

```scala
package edu.qdu.spark.transform

import org.apache.spark.{SparkConf, SparkContext}

object Transform_Repartition {
  def main(args: Array[String]): Unit = {
    val conf = new SparkConf().setAppName("repartition").setMaster("local[*]")
    val sc = new SparkContext(conf)
    val rdd = sc.parallelize(1 to 10)

    println("original")
    println(rdd.partitions.length)
    println("After")
    val reRdd = rdd.repartition(6)
    println(reRdd.partitions.length)
  }
  def addOne(num:Int):Int ={
    num + 1
  }
}

```

```text
original
12
After
6
```

因为我的电脑是十二核的，所以原始是12个分区（12个线程）。通过repartition重新分区使得最后只有6个线程

**reduce**操作

```scala
package edu.qdu.spark.transform

import org.apache.spark.{SparkConf, SparkContext}

object Action_Reduce {
  def main(args: Array[String]): Unit = {
    val conf = new SparkConf().setAppName("map").setMaster("local[2]")
    val sc = new SparkContext(conf)
    val rdd = sc.parallelize(1 to 5)
    val sum = rdd.reduce((x,y)=>x+y)
    println(sum)

    sc.stop()
  }
}

```

```text
15
```

注意这里的语句

`1 to 5` = `1,2,3,4,5`

`(x,y)=>x+y` 数组里面的两个数移出然后相加，最后把相加后的结果加入到数组里面

`1 + 2 = 3`， `1` ，`2`移出，然后将3移进

最后表示的意思是`1`到`5`相加

**First**

取出第一个分区的第一个数据

```scala
package edu.qdu.spark.transform

import org.apache.spark.{SparkConf, SparkContext}

object Action_First {
  def main(args: Array[String]): Unit = {
    val conf = new SparkConf().setAppName("map").setMaster("local[2]")
    val sc = new SparkContext(conf)
    val rdd = sc.parallelize(1 to 3)
    val num = rdd.first() // 取出rdd中第一个分区的第一数据
    println(num)

    sc.stop()
  }
}   

```

```text
1
```

**take**

先从第一个分区中取出数据，如果不够就从第二个分区拿，一次类推

```scala
package edu.qdu.spark.transform

import org.apache.spark.{SparkConf, SparkContext}

object Action_First {
  def main(args: Array[String]): Unit = {
    val conf = new SparkConf().setAppName("first").setMaster("local[3]")
    val sc = new SparkContext(conf)
    val rdd = sc.parallelize(1 to 3)
    val arr = rdd.take(3)
    arr.foreach(println)


    sc.stop()
  }
}   

```

**takeOrdered**按照顺序提取

先对rdd分区中的数据进行排序，排序完毕后，再取数据，取数据的方式同take

只要排序默认为升序

```scala
package edu.qdu.spark.transform

import org.apache.spark.{SparkConf, SparkContext}

object Action_First {
  def main(args: Array[String]): Unit = {
    val conf = new SparkConf().setAppName("takeOrdered").setMaster("local[3]")
    val sc = new SparkContext(conf)
    val rdd = sc.parallelize(List(8,10,1,5))
    val arr = rdd.takeOrdered(3)
    arr.foreach(println)


    sc.stop()
  }
}   

```

```text
1
5
8
```

降序

`val arr = rdd.takeOrdered(3)((x,y)=>y-x)`

**savaAsTextFile**保存文件

保存到本地

```scala
package edu.qdu.spark.transform

import org.apache.spark.{SparkConf, SparkContext}

object Action_Save {
  def main(args: Array[String]): Unit = {
    val conf = new SparkConf().setAppName("takeOrdered").setMaster("local[3]")
    val sc = new SparkContext(conf)
    val rdd = sc.parallelize(List(8,10,1,5))
    rdd.saveAsTextFile("d:/output")

    sc.stop()
  }
}   

```

保存到hadoop上

首先打开hadoop `start-dfs.sh`

```scala
package edu.qdu.spark.transform

import org.apache.spark.{SparkConf, SparkContext}

object Action_Save {
  def main(args: Array[String]): Unit = {
    val conf = new SparkConf().setAppName("takeOrdered").setMaster("local[3]")
    val sc = new SparkContext(conf)
    val rdd = sc.parallelize(List(8,10,1,5))
    rdd.saveAsTextFile("hdfs://spark01:9820/output")

    sc.stop()
  }
}   

```

**countByKey**

```scala
package edu.qdu.spark.transform

import org.apache.spark.{SparkConf, SparkContext}

object Action_CountByKey {
  def main(args: Array[String]): Unit = {
    val conf = new SparkConf().setAppName("takeOrdered").setMaster("local[3]")
    val sc = new SparkContext(conf)
    val rdd = sc.parallelize(List(
      ("a",1),
      ("b",1),
      ("c",1),
      ("a",2),
    ))
    val map = rdd.countByKey()
    map.foreach(println)

    sc.stop()
  }
}   

```

```text
(c,1)
(a,2)
(b,1)
```



- 对于**sparkcore**来说，要进行大数据分析的话就需要放入到`rdd`当中
- 对于 **sparksql**进行数据分析的话，必须把要分析的数据存放到 `dataset`当中
- `dataframe`是一个特殊的`dataset`，`dataframe`和`dataset`都是数据库的一张表

# SparkSql

SparkSql是在SparkCore上的一层外封装

读取文件的方式就和SparkCore的方式不同了

## 读取普通txt文件

```scala
package edu.qdu.spark.sparksql

import org.apache.spark.sql.SparkSession

object SparkSqlDriver {
  def main(args: Array[String]): Unit = {
    // 以前使用sparkcore的时候就需要 conf ... SparkContext的类型
    // 现在使用sparksql就需要另外的模版

    //SparkSession包含了SparkConf和SparkContext
    val spark = SparkSession.builder().appName("sql").master("local[3]").getOrCreate()

    // 读取数据到df/ds
    // 1. 读取普通文本
    val rdd = spark.sparkContext.textFile("input/person.txt").map(line => {
      // line 是行读入
      // line.split 是进行行切分，切分成数组
      val arr = line.split(",")
      Person(arr(0).toInt, arr(1), arr(2), arr(3), arr(4).toInt)
  })
    
    // 隐式转换
    import spark.implicits._
    // 把rdd转换为dataframe
    val df = rdd.toDF()
    // 给df起一个别名
    df.createOrReplaceTempView("person")
    // 写sql语句
    spark.sql("select * from person where age>22").show()
    spark.stop()
}
}

case class Person(val pid:Int, val pname: String,val phone:String, val addr:String,val age:Int)
```



## 对成绩进行排序

```text
孙悟空,数学,95
宋宋,数学,86
婷婷,数学,85
大海,数学,56
宋宋,英语,84
大海,英语,84
婷婷,英语,78
孙悟空,英语,68
大海,语文,94
孙悟空,语文,87
婷婷,语文,65
宋宋,语文,64
```

```scala
package edu.qdu.spark.sparksql

import org.apache.spark.sql.SparkSession

object SparkSql_Top3 {
  def main(args: Array[String]): Unit = {
    val spark = SparkSession.builder().appName("top").master("local[3]").getOrCreate()

    // 首先读取csv文件  给每一列起名
    val sourceDf = spark.read.csv(("input/score.csv")).toDF("name","subject","mark") 
    sourceDf.createOrReplaceTempView("score")
    // row_number()表示排序给出一个序号（同分不并列）
    // rank() 表示同分有并列的情况
    // dense_rank()
    // partition by subject 表示按subject按组分配
    // order by mark desc 表示按照降序排序
    spark.sql("select *,dense_rank()" +"over(partition by subject order by mark desc)rank from score").show()

    spark.stop()
  }
}

```

这里涉及到基本的`sparkSql`的运算语言

```SPARQL
row_number()表示排序给出一个序号（同分不并列）
rank() 表示同分有并列的情况
dense_rank()
partition by subject 表示按subject按组分配
order by mark desc 表示按照降序排序
```

# ZoopKeeper

**软件运行**时候的**数据**存放在`ZoopKeeper`当中，往往我们存储的大数据中的数据是存放在 `Spark`当中

- 无论是kafka集群，还是producer和consumer都依赖于**zookeeper**集群保存一些meta信息，来保证系统可用性。

`ZoopKeeper`是基于投票机制的，选取出来心跳端口与数据端口。所以要想投票机制出现结果一定是需要奇数台机器，避免出现同票情况。

## 命令

**启动**

```text
zkServer.sh start
```

![](https://124newblog-1309411887.cos.ap-nanjing.myqcloud.com/images/202307060850348.png)

**查看状态**

```text
zkServer.sh status
```

![](https://124newblog-1309411887.cos.ap-nanjing.myqcloud.com/images/202307060850350.png)

**停止**

```text
zkServer.sh stop
```



# Kafka

![kafka](https://124newblog-1309411887.cos.ap-nanjing.myqcloud.com/images/202307060850353.png)

基于消息队列，生产者产生数据放入队列当中，消费者进行从队列当中取出数据.

可以在不同机器上面启动生产者与消费者进程

topic----rdd

`replication-factor 2`表示每个分区有两个备份

`partition`分区

`broker`用来查看服务器（只要安装了kafka的服务器就称作为broker)

`group`消费者消费数据的时候,必须要以组的形式进行消费

## 端口

使用的是服务器的9092端口进行操作

## 启动与暂停Kafka

`kafka`严重依赖于`zookeeper`，所以在`kafka`启动之前一定要在每一台服务器之前启动`zookeeper`

**前端启动**

```text
kafka-server-start.sh /home/spark0/apps/kafka/config/server.properties 
```

**后端启动**

注意后面是填入的是`kafka`内的`config/server.properties`文件的路径

```text
kafka-server-start.sh -daemon /home/spark0/apps/kafka/config/server.properties 
```

启动之后通过`jps`查看是否成功启动

![](https://124newblog-1309411887.cos.ap-nanjing.myqcloud.com/images/202307060850351.png)

有显示`kafka`即成功启动

要注意kafka需要每个服务器上都启动，虽然是一个集群但是没有主节点子节点之分。

**脚本简化启动**

因为命令后面带有很长的路径，所以写一个**脚本简化启动**

进入到`kafka`的`bin`文件后创建`start-kafka.sh`脚本

使用`chmod +x start-kafka.sh`给文件添加可执行命令

在sh脚本内填入(正常我们启动的命令)

```shell
/home/spark0/apps/kafka/bin/kafka-server-start.sh -daemon /home/spark0/apps/kafka/config/server.properties
```

之后只需要在`bin`目录下直接使用`start-kafka.sh`即可

**暂停**

```text
kafka-server-stop.sh 
```



## Topic

**创建topic**

```text
 kafka-topics.sh --create --bootstrap-server spark02:9092 --partitions 3 --replication-factor 2 --topic qdu
```

创建topic一定要写明分区数和备份数目以及主题名字

**查看topic**

```text
kafka-topics.sh --list --bootstrap-server spark02:9092
```

**查看topic详情**

```text
kafka-topics.sh --describe --bootstrap-server spark02:9092 --topic qdu
```

![](https://124newblog-1309411887.cos.ap-nanjing.myqcloud.com/images/202307060850352.png)

**删除topic**

```text
bin/kafka-topics.sh --bootstrap-server broker_host:port --delete --topic my_topic_name
```



## 创建生产者进程与消费者进程

验证消费者与生产者进程是否可以正确运行

**在spark02上创建生产者进程**

**使用命令**

`kafka-console-producer.sh --broker-list spark01:9092 --topic qdu`

![](https://124newblog-1309411887.cos.ap-nanjing.myqcloud.com/images/202307060850929.png)

这里的命令`spark01`都是可以任意的，因为`kafka`是一个集群

**在spark03上创建消费者进程**

使用命令

` kafka-console-consumer.sh --bootstrap-server spark01:9092 --topic qdu`

![](https://124newblog-1309411887.cos.ap-nanjing.myqcloud.com/images/202307060850935.png)

可以发现上面的生产者进程与消费者进程实现了消息队列，并且可以生成消息与接受消息

## 利用Scala语法进行对kafka的读写存放数据

使用`scala`操作`kafka`，先从[中央仓库](https://mvnrepository.com/artifact/org.apache.kafka/kafka-clients/2.4.1)中获取kafka-client（客户端）

放入到`pom.xml`当中

```text
<!-- https://mvnrepository.com/artifact/org.apache.kafka/kafka-clients -->
<dependency>
    <groupId>org.apache.kafka</groupId>
    <artifactId>kafka-clients</artifactId>
    <version>2.4.1</version>
</dependency>

```

对工程添加scala框架

右键点击`kafka Add Framework Support`

![](https://124newblog-1309411887.cos.ap-nanjing.myqcloud.com/images/202307060850922.png)

![](https://124newblog-1309411887.cos.ap-nanjing.myqcloud.com/images/202307060850931.png)

**生产者进程**

```scala
package edu.qdu.kafka

import org.apache.kafka.clients.producer.KafkaProducer
import org.apache.kafka.clients.producer.ProducerRecord

import java.util.{HashMap, Map, Properties}


object MyKafkaProducer {
  def main(args: Array[String]): Unit = {
    val props = new Properties()
    props.setProperty("bootstrap.servers","Slave1:9092")
    props.setProperty("key.serializer","org.apache.kafka.common.serialization.StringSerializer")
    props.setProperty("value.serializer","org.apache.kafka.common.serialization.StringSerializer")
    val producer = new KafkaProducer[String,String](props)
    val record = new ProducerRecord[String,String]("qdu","HelloWorld!!!!!")
    producer.send(record)
    producer.close()
  }
}

```



**消费者进程**

```

```



## 利用Java语法进行对kafka进行操作

```java
package edu.qdu.kafka;

import org.apache.kafka.clients.producer.KafkaProducer;
import org.apache.kafka.clients.producer.ProducerRecord;
import org.apache.kafka.common.serialization.StringSerializer;

import java.util.HashMap;
import java.util.Map;

public class QKafkaProducer {
    public static void main(String[] args) {
        Map<String,Object> map = new HashMap<>();
        map.put("bootstrap.servers","spark01:9092");
        // serialiezer 序列化
        map.put("key.serializer","org.apache.kafka.common.serialization.StringSerializer");
        map.put("value.serializer","org.apache.kafka.common.serialization.StringSerializer");
        KafkaProducer<String,String> producer = new KafkaProducer<String, String>(map);

        ProducerRecord<String,String> record = new ProducerRecord<>("qdu","hello");
        producer.send(record);
        producer.close();
    }
}

```

![](https://124newblog-1309411887.cos.ap-nanjing.myqcloud.com/images/202307060850937.png)

可以在终端的消费者进程中看消息

![](https://124newblog-1309411887.cos.ap-nanjing.myqcloud.com/images/202307060850940.png)

**注意**

```text
一定要close，因为消息队列相当于一个缓存，如果不关闭是无法将数据出队列的
```

# Flume 数据采集工具

数据采集有爬虫和Flume。

![Agent component diagram](https://124newblog-1309411887.cos.ap-nanjing.myqcloud.com/images/202307060850224.png)

**source**:负责从外部读取数据，也称为event。每一个event包括了头`（key,value)`与体`(byte)`

source可以从多个外部文件获取数据，而sink只能从一个channel读取数据

**channel** 是缓存

**Sink** 下沉将数据传给外部可以是HDFS等

## 配置

解压然后再Flume文件内创建一个jobs文件夹

### **从flume读取数据到终端上**

在jobs内创建`flume-to-console.conf`文件

```text
a2.sources=r2
a2.channels=c2
a2.sinks=k2

a2.sources.r2.type=netcat
a2.sources.r2.bind=localhost
a2.sources.r2.port=8888

a2.channels.c2.type=memory
a2.channels.c2.capacity=1000
a2.channels.c2.transactionCapcity=100

a2.sinks.k2.type=logger

a2.sources.r2.channels=c2
a2.sinks.k2.channel=c2
```

使用命令启动从flume读取数据到终端上

```text
bin/flume-ng agent -n a2 -c conf -f jobs/flume-to-console.conf -Dflume.root.logger=INFO,console
```

-f 表示指向路径



![](https://124newblog-1309411887.cos.ap-nanjing.myqcloud.com/images/202307060850235.png)

可以看到主机的8888端口打开准备接受消息

所以我们只需要在8888端口中传入消息既可以接受到消息了

命令

```text
nc localhost 8888
```

表示连接本主机的8888端口

![](https://124newblog-1309411887.cos.ap-nanjing.myqcloud.com/images/202307060850250.png)

传递一个消息

![](https://124newblog-1309411887.cos.ap-nanjing.myqcloud.com/images/202307060850290.png)

可以看到flume`source`读入到了一个数据`abc`，并且包装成为一个event形式

### **从文件中读取数据到终端**

不可行

**从文件中读取数据到kafka当中**

注意这里使用`flume`的`source`模块读取文件，然后直接放入到`kafka`当中，使用的是`kafka-channel`,不再有`sink`的操作

文件配置`file-to-kafka`

文件配置可以参考https://flume.apache.org/releases/content/1.11.0/FlumeUserGuide.html#file-channel中的`kafka channel`文件

```text
a2.sources=r2
a2.channels=c2

a2.sources.r2.type=taildir
a2.sources.r2.positionFile = /home/spark0/apps/flume/taildir_position.json
a2.sources.r2.filegroups = f2
a2.sources.r2.filegroups.f2 = /home/spark0/mock.log

a2.channels.c2.type = org.apache.flume.channel.kafka.KafkaChannel
a2.channels.c2.kafka.bootstrap.servers = spark01:9092,spark02:9092,spark03:9092
a2.channels.c2.kafka.topic = qdu

a2.sources.r2.channels=c2

```

上面的配置文件我们知道应该用`/home/spark0/mock.log`中读取文件

然后我们将信息传到`kafka`当中

首先启动`file-to-kafka`文件

```text
 bin/flume-ng agent -n a2 -c conf -f jobs/file-to-kafka.con
```

在`mock.log`文件中写入内容

```text
echo cjy > mock.log
```

最后启动`kafka`的消费进程

```text
kafka-console-consumer.sh --bootstrap-server spark03:9092 --topic qdu
```

可以发现在 `mock.log`里面读取到了`mock.log`的文件

![](https://124newblog-1309411887.cos.ap-nanjing.myqcloud.com/images/202307060850559.png)

# Redis

Redis是用C语言开发的一个开源的高性能键值对（key-value）数据库。它通过提供多种键值数据类型来适应不同场景下的存储需求，

## 配置

应为使用的是C语言开发，所以还要安装gcc,使用 `sudo yum install gcc-c++ -y`

进入到reids_src``使用 `make`进行编译

编译完之后，` make install PREFIX=/home/spark0/apps/redis`

可以发现`apps`上出现了`redis`

将`redis_src`中的`redis.conf`拷贝到`redis`

进入到`redis`当中运行 `bin/redis-server `

![](https://124newblog-1309411887.cos.ap-nanjing.myqcloud.com/images/202307060850572.png)

## 命令

**启动**

*前台运行*

到`redis`目录下

相对路径

```text
 bin/redis-server
```

绝对路径

```text
/home/spark0/apps/redis/ bin/redis-server
```

![](https://124newblog-1309411887.cos.ap-nanjing.myqcloud.com/images/202307060850605.png)

*后台运行*

修改配置文件

允许远程连接

![](https://124newblog-1309411887.cos.ap-nanjing.myqcloud.com/images/202307060850065.png)

允许后台运行

![](https://124newblog-1309411887.cos.ap-nanjing.myqcloud.com/images/202307060850280.png)

我们的`redis`使用的是6379端口号

![](https://124newblog-1309411887.cos.ap-nanjing.myqcloud.com/images/202307060850466.png)

**使用后台运行**

命令

相对路径

```scala
bin/redis-server redis.conf
```

绝对路径

```scala
/home/spark0/apps/redis/bin/redis-server redis.conf
```

![](https://124newblog-1309411887.cos.ap-nanjing.myqcloud.com/images/202307060850549.png)

**启动redis控制台**

因为只在一台服务器上安装所以我们需要克隆一个对话

使用命令

```text
/home/spark0/apps/redis/bin/redis-cli
```

![](https://124newblog-1309411887.cos.ap-nanjing.myqcloud.com/images/202307060850621.png)

127.0.0.1表示连接到了本机的`redis`

可以使用用命令连接spark01

```text
/home/spark0/apps/redis/bin/redis-cli -h spark01
```

![](https://124newblog-1309411887.cos.ap-nanjing.myqcloud.com/images/202307060850689.png)

## 数据类型

### **字符串类型**

存放数据

```shell
set name cjy
```

获取数据

```shell
get name
```

![](https://124newblog-1309411887.cos.ap-nanjing.myqcloud.com/images/202307060850753.png)

自增

```shell
incr age
```

自减

```shell
decr age
```

自定义增加

```shell
incrby age 3
```

自定义减少

```shell
decrby age 3
```

![](https://124newblog-1309411887.cos.ap-nanjing.myqcloud.com/images/202307060850801.png)

**散列类型**

存放对象的数据类型

![](https://124newblog-1309411887.cos.ap-nanjing.myqcloud.com/images/202307060850910.png)

对一个对象进行操作的

-  hset
   在redis中，命令是不区分大小写，但是key区分大小写 

```shell
hset student name cjy
```

![image-20230702095432597](https://124newblog-1309411887.cos.ap-nanjing.myqcloud.com/images/202307060850975.png)

- hget

```shell
hget student age
```

![](https://124newblog-1309411887.cos.ap-nanjing.myqcloud.com/images/202307060850053.png)



- hmset

```shell
hmet person name cjy gender man age 21
```

![](https://124newblog-1309411887.cos.ap-nanjing.myqcloud.com/images/202307060850126.png)

- hmget

```shell
hmget person name gender age
```

![](https://124newblog-1309411887.cos.ap-nanjing.myqcloud.com/images/202307060850183.png)

- hexists 

```shell
hexists person num
```

![](https://124newblog-1309411887.cos.ap-nanjing.myqcloud.com/images/202307060850249.png)

- expire 设定过期时间

  ```shell
  expire FP 10
  ```

  ![](https://124newblog-1309411887.cos.ap-nanjing.myqcloud.com/images/202307060850350.png)

  `ttl` 用来检测过期时间

存在的话返回的1，不存在的话返回0

- flushdb

  清空数据库



## 使用scala语言操作Redis

```scala
package edu.qdu.redis

import redis.clients.jedis.Jedis

import java.util
import scala.collection.convert.ImplicitConversions.`map AsJavaMap`


object RedisDriver {
  def main(args: Array[String]): Unit = {
    val jedis = new Jedis("spark01",6379)

    // 存放数据与读取数据
    jedis.set("university","qdu")
    val uni = jedis.get("university")
    println(uni)

    // 自增与自定义增加
    jedis.set("age","21")
    jedis.incr("age")
    jedis.incrBy("age",22L)

    // hash操作
    // hset function
    jedis.hset("student","name","cjy")
    jedis.hset("student","gender","man")
    jedis.hset("student","age","21")

    val FJS_personalFile = new util.HashMap[String,String]()
    FJS_personalFile.put("father","cjy")
    FJS_personalFile.put("elder_bother","cjj")
    jedis.hmset("FJS_personalFile",FJS_personalFile)



//    // hmget function
    val list1 = jedis.hmget("student","name","gender","age")
    list1.forEach(item=>println(item))

    // list function
    jedis.lpush("names","cjy","cjc","czs")

    val list2 = jedis.lrange("names",0,-1);
    jedis.close()
  }
}

```

## 使用springboot操作Redis

```java
package edu.qdu.controller;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class IndexController {

    @Autowired
    private RedisTemplate<String,String> redisTemplate;

    @GetMapping("/")
    public String homePage(){
        // opsForValue()： 操作字符串数据类型
        // opsForList(): 用来操作链表
        // opsForHash(): 用来操作hash

        // 字符串操作
        redisTemplate.opsForValue().set("FP","baby_shark");

        // 链表操作
        // leftPush 是每次将数据都放在头部
        redisTemplate.opsForList().leftPush("Ordered","A");
        redisTemplate.opsForList().leftPush("Ordered","B");
        redisTemplate.opsForList().leftPush("Ordered","C");

        // Hash操作
        redisTemplate.opsForHash().put("Caijy","age","21");
        redisTemplate.opsForHash().put("Caijy","bother","Caijc");
        redisTemplate.opsForHash().put("Caijy","pet","Dog");
        return "ok";
    }
}

```

![](https://124newblog-1309411887.cos.ap-nanjing.myqcloud.com/images/202307060850425.png)

# SparkStreaming

**流式计算框架**

流式处理与批处理的区别是：

- 批处理是一次处理大量数据，通常按照计划或固定间隔。流式处理是**实时**处理数据，随着数据生成或输入系统而进行。
- 批处理处理所有或大部分数据，而流式处理处理滚动窗口或最新记录的数据。
- 批处理先收集一批数据，然后一次性处理。流式处理在数据进入系统时实时处理，没有收集和处理之间的等待时间。

## 使用scala操作SparkStreaming

```scala
package edu.qdu.spark

import org.apache.spark.SparkConf
import org.apache.spark.streaming.{Seconds, StreamingContext}

object SparkStreamingDriver {
  def main(args: Array[String]): Unit = {
    val conf = new SparkConf().setAppName("count").setMaster("local[3]")
    // 3秒钟产生一个rdd
    val ssc = new StreamingContext(conf,Seconds(3))

    val ds = ssc.socketTextStream("spark01",8888)
    // flatMap 只针对最新的rdd进行操作
    val res = ds.flatMap(_.split(",")).map((_,1)).reduceByKey(_+_)

    res.print()
    // 开始读取数据
    ssc.start()
    // 只有执行下面这句话才可以不停的运行
    ssc.awaitTermination()
  }
}

```

启动服务器端口

```shell
nc -lp 8888
```

![](https://124newblog-1309411887.cos.ap-nanjing.myqcloud.com/images/202307060850487.png)

可以在程序的终端发现读取到了服务器终端的命令

![](https://124newblog-1309411887.cos.ap-nanjing.myqcloud.com/images/202307060850619.png)

要注意的是 `awaitTermination()`

原文解释

Wait for the execution to stop. Any exceptions that occurs during the execution will be thrown in this thread.

这个函数记住的是整个函数体内的所有的operation操作，并且不断的执行这些操作。

比如上面我们的程序，operation（中文叫做算子）有`ssc.socketTextStream("spark01",8888)`,`ds.flatMap(_.split(",")).map((_,1)).reduceByKey(_+_)`

`res.print()`,在不断等terminal的信息之后回不断执行上面这些操作

## 在SparkSteaming使用scala语言读取kafka数据

```scala
package edu.qdu.spark.streaming

import org.apache.kafka.common.serialization.StringDeserializer
import org.apache.spark.SparkConf
import org.apache.spark.streaming.kafka010.{ConsumerStrategies, KafkaUtils, LocationStrategies}
import org.apache.spark.streaming.{Seconds, StreamingContext}

import scala.collection.mutable
import scala.sys.SystemProperties.headless.set

object SparkStreamingFromKafka {
  def main(args: Array[String]): Unit = {
    val conf = new SparkConf().setAppName("kafka").setMaster("local[3]")
    val ssc = new StreamingContext(conf,Seconds(3))
    println("test_point_1")

    val map = new mutable.HashMap[String,String]()
    map.put("bootstrap.servers","spark01:9092")
    map.put("group.id","qdu")
    map.put("key.deserializer","org.apache.kafka.common.serialization.StringDeserializer")
    map.put("value.deserializer","org.apache.kafka.common.serialization.StringDeserializer")

    println("test_point_2")

    val kafkaDs = KafkaUtils.createDirectStream(ssc,
      LocationStrategies.PreferConsistent,
      ConsumerStrategies.Subscribe[String,String](Set("qdu"),map)
    )

    val sourceDs = kafkaDs.map(_.value())
    sourceDs.print()
    ssc.start()
    println("test_point_3")
    ssc.awaitTermination()
  }
}

```

如果这里的`test_point_3`没有显示出来的话，说明的是连接上的问题，需要看一下本地的hosts文件进行修改

在服务器上启动生产者进程

`kafka-console-producer.sh --broker-list spark01:9092 --topic qdu`

![](https://124newblog-1309411887.cos.ap-nanjing.myqcloud.com/images/202307060850664.png)

在程序上的终端我们可以显示

![](https://124newblog-1309411887.cos.ap-nanjing.myqcloud.com/images/202307060850798.png)

## 使用Flum采集数据到kafka当中，然后使用SparkSteaming读取Kafka数据

先启动服务器的的Flum采集器

`bin/flume-ng agent -n a2 -c conf -f jobs/file-to-kafka.conf`

![](https://124newblog-1309411887.cos.ap-nanjing.myqcloud.com/images/202307060850908.png)

修改`mock.log`文件内容

![](https://124newblog-1309411887.cos.ap-nanjing.myqcloud.com/images/202307060850969.png)

启动程序，在程序终端上可以查看到`mock.log`中的内容

![](https://124newblog-1309411887.cos.ap-nanjing.myqcloud.com/images/202307060850041.png)

# 项目实践报告

**静态推荐**

根据Mongo中的Rating数据，使用Spark统计如下指标，通常用于静态推荐。

- 统计所有历史数据当中每个商品的评分数次数，并存储到MongoDB的RateMoreProducts集合中。

- 统计以月为单位拟每个商品的评分数次数，并存储到MongoDB的RateMoreRecentlyProducts集合中。

- 统计每个商品的平均评分的分数，并存储到MongoDB的AverageProducts集合中。

```scala
package edu.qdu.statics

import com.mongodb.casbah.{MongoClient, MongoClientURI}
import com.mongodb.casbah.commons.MongoDBObject
import org.apache.spark.sql.{DataFrame, SaveMode, SparkSession}

import java.text.SimpleDateFormat
import java.util.Date

/**
 * 基于评分统计
 * ● 统计所有历史数据当中每个商品的评分数次数，并存储到MongoDB的RateMoreProducts集合中。
 * ● 统计以月为单位拟每个商品的评分数次数，并存储到MongoDB的RateMoreRecentlyProducts集合中。
 * ● 统计每个商品的平均评分的分数，并存储到MongoDB的AverageProducts集合中。
 */
//封装mongoDB的连接信息
case class MongoConfig(url:String,db:String)
//封装用户商品评分数据
case class Rating(userID:Int,productID:Int,rating:Double,timestamp:Long)

/**
 * 静态推荐
 */
object StaticsRecommender {
  def main(args: Array[String]): Unit = {
    //Mongo的连接配置
    val configMap = Map(
      "url" -> "mongodb://qianfeng01:27017/recommender",
      "db" -> "recommender"
    )

    //定义集合常量
    val RATING_COLLECTION = "Rating"
    val RATE_MORE_COLLECTION = "RateMoreProducts"
    val RATE_MORE_RECENTLY_COLLECTION = "RateMoreRecentlyProducts"
    val AVERAGE_COLLECTION = "AverageProducts"

    //定义一个隐士参数
    implicit val config = MongoConfig(configMap.getOrElse("url", ""), configMap.getOrElse("db", ""))

    //获取SparkSQL的上下文
    val spark = SparkSession.builder()
      .appName("dataloader")
      .master("local[*]") //如果往服务器部署，需要修改一下即可
      .getOrCreate()

    //引入SparkSQL的隐士转换
    import spark.implicits._
    //加载Rating数据集
    val ratingDF = spark
      .read
      .option("uri", config.url)
      .option("collection", RATING_COLLECTION)
      .format("com.mongodb.spark.sql")
      .load() //返回DataFrame
      .as[Rating] //将DataFrame中的每一行转化成一个Rating对象，将DataFrame转化为Dataset[Rating]
      .toDF() 

    //将ratingDF注册成表
    ratingDF.createOrReplaceTempView("ratings")

    /**
     * 1、统计所有历史数据当中每个商品的评分数次数，并存储到MongoDB的RateMoreProducts集合中。
     */
    val rateMoreDF = spark.sql(
      """
        |select
        |productID productId,
        |count(*) count
        |from ratings
        |group by productID
        |""".stripMargin)
    rateMoreDF.show()

    //将历史商品中每个商品的评分次数统计存储到MongoDB中
    writeDataToMongoDB(rateMoreDF,RATE_MORE_COLLECTION)

    /**
     * 2、统计以月为单位拟每个商品的评分数次数，并存储到MongoDB的RateMoreRecentlyProducts集合中。
     */
    //自定义一个函数，将时间戳转换为YYYYMM
    val sdf = new SimpleDateFormat("YYYYMM")
    spark.udf.register("getMonth",(timeStamp:Long)=>sdf.format(new Date(timeStamp * 1000)))
    /*
    //计算每月每商品的评分次数---方法一
    val rateMoreRecentlyDF = spark.sql(
      """
        |select
        |productID productId,
        |count(*) count,
        |getMonth(timestamp) yearmonth
        |from ratings
        |group by productID,getMonth(timestamp)
        |""".stripMargin)*/
    //计算每月每商品的评分次数
    val rateMoreRecentlyDF = spark.sql(
      """
        |select
        |productID productId,
        |count(*) count,
        |from_unixtime(cast(timestamp as bigint),"yyyyMM") yearmonth
        |from ratings
        |group by productID,from_unixtime(cast(timestamp as bigint),"yyyyMM")
        |""".stripMargin)
    rateMoreRecentlyDF.show()

    //将最近的年月商品评分次数存储到MongoDB中
    writeDataToMongoDB(rateMoreRecentlyDF,RATE_MORE_RECENTLY_COLLECTION)

    /**
     * 3、统计每个商品的平均评分的分数，并存储到MongoDB的AverageProducts集合中。
     */
    val avgDF = spark.sql(
      """
        |select
        |productID productId,
        |avg(rating) avg
        |from ratings
        |group by productID
        |""".stripMargin)
    avgDF.show()

    //将avgDF存储到Mongo中
    writeDataToMongoDB(avgDF,AVERAGE_COLLECTION)

    //关闭spark
    spark.stop()
  }

  /**
   * 将productDF和ratingDF写入数库的集合中
   * @param spark
   * @param productDF
   * @param ratingDF
   * @param config
   */
  def writeDataToMongoDB(df: DataFrame,col:String)(implicit config:MongoConfig): Unit ={
    //获取Mongo的client
    val client = MongoClient(MongoClientURI(config.url))
    //获取操作集合
    val collection = client(config.db)(col)

    //删除集合
    collection.dropCollection()

    //将productDF写入collection中
      df
      .write
      .option("uri",config.url)
      .option("collection",col)
      .mode(SaveMode.Overwrite)
      .format("com.mongodb.spark.sql")
      .save()

    //对数据创建索引
    collection.createIndex(MongoDBObject("productID"->1))
  }
}
```

代码解释

```scala
 //加载Rating数据集
    val ratingDF = spark
      .read
      .option("uri", config.url)
      .option("collection", RATING_COLLECTION)
      .format("com.mongodb.spark.sql")
      .load() //返回DataFrame
      .as[Rating] //将DataFrame中的每一行转化成一个Rating对象，将DataFrame转化为Dataset[Rating]
      .toDF() 
```

Dataset是强类型的数据集，而DataFrame是一种无类型的数据集。

```scala
//计算每月每商品的评分次数---方法二
    val rateMoreRecentlyDF = spark.sql(
      """
        |select
        |productID productId,
        |count(*) count,
        |from_unixtime(cast(timestamp as bigint),"yyyyMM") yearmonth
        |from ratings
        |group by productID,from_unixtime(cast(timestamp as bigint),"yyyyMM")
        |""".stripMargin)
    rateMoreRecentlyDF.show()
```

这段代码是使用Spark SQL对数据集进行分组聚合，统计每个产品在每个月份的评分总数。具体解释如下：

首先，使用`|`创建一个多行字符串，该字符串包含了一个Spark SQL的查询语句。在这个查询语句中，使用`select`关键字选择需要查询的列，包括产品ID、评分总数和年月份（用于分组聚合）。

```scala
  |select
  |productID productId,
  |count(*) count,
  |from_unixtime(cast(timestamp as bigint),"yyyyMM") yearmonth
```

`from_unixtime`和`cast`函数是Spark SQL中的函数，用于将Unix时间戳转换为日期格式和将数据类型从一种转换为另一种类型。具体解释如下：

`cast(timestamp as bigint)`将`timestamp`列从原来的数据类型转换为`bigint`类型，即将Unix时间戳表示的秒数转换为`bigint`类型的毫秒数。

`from_unixtime(cast(timestamp as bigint),"yyyyMM")`将`timestamp`列中的毫秒数转换为日期格式，格式为`yyyyMM`，表示年份和月份。其中，第一个参数是要转换为日期格式的列，第二个参数是日期格式的字符串。

其次，使用`from`关键字指定数据集的名称为`ratings`，表示从`ratings`数据集中查询数据。

```scala
  |from ratings
```

然后，使用`group by`关键字按照产品ID和年月份进行分组聚合，统计每个产品在每个月份的评分总数。

```scala
  |group by productID,from_unixtime(cast(timestamp as bigint),"yyyyMM")
```

最后，将这个查询语句作为参数传递给Spark SQL的`sql`方法，执行查询，并将结果保存在一个`DataFrame`对象中。

```scala
  spark.sql(
    """
      |select
      |productID productId,
      |count(*) count,
      |from_unixtime(cast(timestamp as bigint),"yyyyMM") yearmonth
      |from ratings
      |group by productID,from_unixtime(cast(timestamp as bigint),"yyyyMM")
      |""".stripMargin)
```

这段代码的作用是对数据集进行分组聚合，统计每个产品在每个月份的评分总数，为后续的数据分析和可视化打下基础。注意，这里使用了Spark SQL的查询语法，将查询结果转换成一个`DataFrame`对象。

**离线推荐**

OfflineRecommender 服务主要是**采用 ALS 作为协同过滤算法**，根据 MongoDB 中的**用户评分表**计算离线的用户商品推荐列表以及商品相似度矩阵。

**显式数据与隐式数据(Explicit data and implicit data)**

1. **显式数据**(Explicit Data) 是指那些有评价得分的数据，比如对电影的评分。此类数据明确指出用户对物品的喜好程度，但往往很难获取到。
2. **隐式数据**(Implicit Data) 是指从用户行为中收集到的数据，缺少评分或评分所必须的特定行为。这可能是一个用户购买了某个物品、重复播放了多少次歌曲、观看某部电影多长时间以及阅读某篇文章的时长等等。此类数据优点是数据量大，缺点是噪音较多并且往往含义不明显。举个例子，通过给商品打星的数量，我们知道 1 表示用户不喜欢该商品，5 表示用户非常喜爱。用户播放了某歌曲，我们推测用户可能喜欢该歌曲或者讨厌该歌曲，也可能介于两者之间。如果用户没有播放某歌曲，有可能是因为用户不喜欢它，也可能只是不知道这首歌曲的存在。

而ALS算法就是可以处理显式数据和隐式数据的一种算法，区别就是在于代价函数与信任度的定义

```scala
package edu.qdu.offline

import com.mongodb.casbah.Imports.{MongoClientURI, MongoDBObject}
import com.mongodb.casbah.MongoClient
import org.apache.spark.mllib.recommendation.ALS
import org.apache.spark.mllib.recommendation.Rating
import org.apache.spark.rdd.RDD
import org.apache.spark.sql.{DataFrame, SaveMode, SparkSession}
import org.jblas.DoubleMatrix


/**
 * 离线推荐服务和相似商品的加工
 */

//封装评分数据
//封装用户商品评分数据
case class ProductRating(userId:Int,productId:Int,rating:Double,timestamp:Long)
//封装mongoDB的连接信息
case class MongoConfig(url:String,db:String)


//封装商品类
case class Recommendation(productId: Int,score:Double)
//封装产品相似矩阵
case class ProductRecs(productId:Int,recs:Seq[Recommendation])
//封装用户推荐列表
case class UserRecs(userId:Int,recs:Seq[Recommendation])

object OfflineRecommender {
  def main(args: Array[String]): Unit = {

    //Mongo的连接配置
    val configMap = Map(
      "url" -> "mongodb://spark01:27017/recommender",
      "db" -> "recommender"
    )

    //定义一个隐士参数
    implicit val config = MongoConfig(configMap.getOrElse("url", ""), configMap.getOrElse("db", ""))

    //定义集合常量
    val USER_RECS_COLLECTION = "UserRecs"   //用户推荐存储在MongoDB中的集合
    val PRODUCT_RECS_COLLECTION = "ProductRecs"   //商品相似矩阵存储在MongoDB中的集合
    val RATING_COLLECTION = "Rating"  //用户商品评分集合
    val MAX_RECOMMEND_NUMBER = 20   //最大推荐数量

    //获取SparkSQL的上下文
    val spark = SparkSession.builder()
      .appName("offline-recommender")
      .master("local[*]") //如果往服务器部署，需要修改一下即可
      .getOrCreate()

    //加载Ratings的数据，然后转换成RDD，并将其进行缓存
    import spark.implicits._
    val ratingRDD = spark
      .read
      .option("uri", config.url)
      .option("collection", RATING_COLLECTION)
      .format("com.mongodb.spark.sql")
      .load() //返回DataFrame
      .as[ProductRating] //将df转换成DS
      .rdd //将ds转换成RDD
      .map(line => (line.userId, line.productId, line.rating)) //构建用户、商品、打分三元组
      .cache()   //缓存
    //ratingRDD.foreach(println(_))
    //构建训练集 --- 封装的Rating一定是recommendation包下的
    val trainData = ratingRDD.map(x => org.apache.spark.mllib.recommendation.Rating(x._1, x._2, x._3))

    //获取userId和productId的去重列表
    val userRDD = ratingRDD.map(x => x._1).distinct()   //去重后的用户列表
    val productRDD = ratingRDD.map(x => x._2).distinct()  //去重后的商品列表
    //使用去重后的userRDD和productRDD进行笛卡尔积计算，形成一个空的用户商品矩阵
    val userProduct = userRDD.cartesian(productRDD)
    //userProduct.foreach(println(_))

    //构建ALS模型
    val (rank,iterations,regex) = (10,10,0.01)   //分别为隐士因子个数；迭代次数；正则化系数
    //引入mllib包下的ALS --->使用训练集来训练ALS模型
    val alsMode = ALS.train(trainData,rank,iterations,regex)

    //预测 --->使用userProduct来进行预测
    val preRDD = alsMode.predict(userProduct)

    //对预测结果集进行过滤和封装
    val userRecsDF = preRDD //RDD[Rating]
      .filter(x => x.rating > 0) //将预测的评分大于0的商品过滤出来
      .map(rating => {
        (rating.user, (rating.product, rating.rating)) //将数据封装成 (userid,(productId,rating))
      })
      .groupByKey() //按照user来进行分组 //(12，Iterable[(),(),(),(),()])
      .map(x => {
        //封装成最终的结果数据
        UserRecs(
          x._1, //用户Id
          x._2.toList //将Iterable[(),(),(),(),()]转换成List
            .sortWith(_._2 > _._2) //使用得分进行降序排序；；大于是降序
            .take(MAX_RECOMMEND_NUMBER) //获取得分最高的前20个商品
            .map(x => Recommendation(x._1, x._2)) //将商品Id和预测得分封装到Recommendation中
        )
      })
      .toDF()
    userRecsDF.show()
    //将数据写出MongoDB中UserRecs集合中
    writeDataToMongoDB(userRecsDF,USER_RECS_COLLECTION)


    //商品相似矩阵
    //1、获取商品的特征向量 --->10维度
    val productFeatures:RDD[(Int,DoubleMatrix)] = alsMode.productFeatures
      .map(x => {
        (x._1, new DoubleMatrix(x._2))  //(商品ID，对应商品的特征向量)
      })
    //2、获取每一个商品的余弦相似度
    val productRecsDF = productFeatures.cartesian(productFeatures) //商品特征向量笛卡尔积
      .filter(x => x._1._1 != x._2._1) //过滤掉两个相同的商品
      .map(x => {
        val consin = consinSim(x._1._2, x._2._2) //使用商品的特征向量调用consinSim方法
        //封装返回
        (x._1._1, (x._2._1, consin)) //(p1商品Id，(p2商品Id,p1和p2的余弦相似度))
      })
      .filter(x => x._2._2 > 0.6) //过滤余弦相似度小于0.6的商品
      .groupByKey() //根据商品ID(x._1._1)来进行分组
      .map(x => {
        //封装返回的数据ProductRecs
        ProductRecs(
          x._1, //商品ID
          x._2
            .toList
            .map(x => Recommendation(x._1, x._2)) //封装(商品Id，余弦相似度)
        )
      })
      .toDF()
    //productRecsDF.show()

    //将商品的相似矩阵存储到MongoDB中，--->已被实时推荐需要
    writeDataToMongoDB(productRecsDF,PRODUCT_RECS_COLLECTION)

    //关闭spark
    spark.stop()
  }

  /**
   * 将productDF和ratingDF写入数库的集合中
   * @param spark
   * @param productDF
   * @param ratingDF
   * @param config
   */
  def writeDataToMongoDB(df: DataFrame,col:String)(implicit config:MongoConfig): Unit ={
    //获取Mongo的client
    val client = MongoClient(MongoClientURI(config.url))
    //获取操作集合
    val collection = client(config.db)(col)

    //删除集合
    collection.dropCollection()

    //将productDF写入collection中
    df
      .write
      .option("uri",config.url)
      .option("collection",col)
      .mode(SaveMode.Overwrite)
      .format("com.mongodb.spark.sql")
      .save()
  }

  /**
   * 余弦相似度
   * @param product1
   * @param product2
   */
  def consinSim(product1:DoubleMatrix,product2:DoubleMatrix): Double = {
    //相似公式：y = (DXi * DYi)的加和值 / (DXi平方和 + DYi平方和)   i属于(1-n)
    product1.dot(product2) / (product1.norm2() * product2.norm2())
  }
}
```

这段代码实现了一个基于离线推荐算法的推荐系统，主要包括以下几个步骤：

1. 定义了三个样例类：`ProductRating`表示用户对商品的评分数据，`MongoConfig`表示MongoDB的连接信息，`Recommendation`表示推荐结果。

2. 读取MongoDB中的评分数据，并将其转换为`RDD[org.apache.spark.mllib.recommendation.Rating]`类型的训练数据。同时，根据评分数据获取用户和商品的去重列表，并使用笛卡尔积构建一个空的用户商品矩阵。

3. 使用`ALS`算法构建推荐模型，并使用训练数据进行模型训练。

4. 使用模型对用户商品矩阵进行预测，得到预测评分结果，然后过滤掉评分为0的商品，按照用户进行分组，取得分最高的前20个商品，并将结果封装成`UserRecs`对象，最终转换为DataFrame。

5. 将`UserRecs`数据写入MongoDB中的`UserRecs`集合中。

6. 使用商品特征向量计算商品之间的余弦相似度，并根据相似度过滤和封装相似商品数据，最终转换为DataFrame。

7. 将相似商品数据写入MongoDB中的`ProductRecs`集合中。

8. 定义了一个`writeDataToMongoDB`函数，用于将DataFrame数据写入MongoDB中的指定集合。

9. 定义了一个`consinSim`函数，用于计算两个商品特征向量之间的余弦相似度。

整个推荐过程中，使用了SparkSQL和MongoDB等技术。其中，离线推荐算法采用了ALS算法，并使用了`org.apache.spark.mllib.recommendation`包中的相关API。

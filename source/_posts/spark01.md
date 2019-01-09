---
title: spark01
tags:
  - null
  - null
date: 2018-11-20 11:45:00
categories:
top:
---

<!-- more -->

# 回顾

- 大数据生态: 解决的问题: 分布式存储和分布式计算

​	Google:GFS / MapReduce / Bigtable

- 基于三篇论文,产生了hadoop

​	hdfs: 分布式的存储

​	MapReduce: 计算框架(离线计算)

​	facebook创建hive,SQL-ON_Hadoop,数据仓库的标准

​	(以上的效率低,延时高)

​	基于bigtable的hbase出现,(百万级别列,上10亿的行)

- 辅助工具:

​	flume: 日志收集工具

​	sqoop:rdb关系型数据库  <---> hdfs

​	Kafka: 消息队列

​	azkaban:作业的调度

- zookeeper:

> **zookeeper:**zookeeper做资源协调调度 比如hdfs-ha选举
>
> **azkaban**:做任务调度task，比如mrjob的调度
>
> 再简单一点zookeeper调度的是集群内部的各个节点
>
> azkaban调度的是俺们自己写的mr job

- 大数据计算

  - 三大计算中心
    1. 离线计算（离线批处理）mapreduce 慢
    2. 实时计算（事件计算） storm flink
    3. 准实时计算
  - 三大计算引擎
    1. 交互式计算引擎 hbase
    2. 图计算引擎 hame graph
    3. 机器学习引擎 mathout

  > spark:**基于内存的一站式的通用计算框架,**
  >
  > 数据来源: Hdfs,mysql,local,kafka,hbase,hive
  >
  > 资源调度框架: yarn mesos standalone
  >
  > sparkCore :核心RDD(弹性式分布式数据集): 离线计算
  >
  > sparkSql:
  >
  > sparkStreaming:准实时计算
  >
  > sparkGrapht:图计算
  >
  > sparkLLib: 机器学习
  >
  > spark shell : 交互式计算

> google开源的datacloud 一站式:Apache Beam







# Spark

> MRv1:
>
>  	1. 可扩展差(jobTracker:即负责资源调度,又负责任务管理)
>  	2. 可用性差(单点故障)
>  	3. 资源利用率底,一些 Task 并不能充分利用 slot，而其他 Task也无法使用这些空闲的资源。
>  	4. 不支持多种MR框架

> MR2:
>
> 运行时环境被重构了。JobTracker被拆分成了通用的
> 资源调度平台（ResourceManager，简称 RM）、,项目经理(整体资源分配,监控)
> 节点管理器（NodeManager）、(单台资源的调度)
> 负责各个计算框架的任务调度模型（ApplicationMaster，简称 AM）。

> 由于对 HDFS 的频繁操作**（包括计算结果持久化、数据备份、资源下载及 Shuffle 等）导致磁盘 I/O 成为系统性能的瓶颈**，因此只适用于离线数据处理或批处理，而不能支持对迭代式、交互式、流式数据的处理。



## 优势

1. 减少磁盘 I/O

   > Spark 允许将 map 端的中间输出和结果存储在内存中，reduce 端在拉取中间结果时避免了大量的磁盘 I/O。
   >
   > Spark 将应用程序上传的源文件缓冲到 Driver 本地文件服务的内存中，当 Executor 执行任务时直接从 Driver 的内中读取，也节省了大量的磁盘 I/O。

2. 增加并行度

   > 由于将中间结果写到磁盘与从磁盘读取中间结果属于不同的环节，Hadoop 将它们简单的通过串行执行衔接起来。
   >
   > Spark 把不同的环节抽象为 Stage，允许多个 Stage 既可以串行执行，又可以并行执行。

3. 避免重新计算

   > 当 Stage 中某个分区的 Task 执行失败后，会重新对此 Stage 调度，但在重新
   > 调度的时候会过滤已经执行成功的分区任务，所以不会造成重复计算和资源浪费。

4. 可选的 Shuffle 和排序

5. 灵活的内存管理策略

> **Spark 是 MapReduce 的替代方案，而且兼容 HDFS、Hive，可融入 Hadoop 的生态系统，以**
> **弥补 MapReduce 的不足。**



## 概念

> 官网：http://spark.apache.org/
>
> Spark 是一种快速、通用、可扩展的大数据分析引擎
>
> 2009 年诞生于加州大学伯克利分校 AMPLab
>
> 2010 年开源
>
> 2013 年 6 月成为 Apache 孵化项目
>
> 2014 年 2 月成为 Apache 顶级项目



## 应用场景

1. 复杂的批量处理（Batch Data Processing），偏重点在于处理海量数据的能力，至于处理
   速度可忍受，通常的时间可能是在数十分钟到数小时；
2. 基于历史数据的交互式查询（Interactive Query），通常的时间在数十秒到数十分钟之间
3. 基于实时数据流的数据处理（Streaming Data Processing），通常在数百毫秒到数秒之间
   目前对以上三种场景需求都有比较成熟的处理框架：

> 第一种情况可以用 Hadoop 的 MapReduce 来进行批量海量数据处理
> 第二种情况可以 Impala、Kylin 进行交互式查询
> 第三中情况可以用 Storm 分布式处理框架处理实时流式数据

> 以上三者都是比较独立，各自一套维护成本比较高，而 Spark 的出现能够一站式平台满意以
> 上需求。
> 第一种情况使用 Spark Core 解决
> 第二种情况使用 Spark SQL 解决
> 第三种情况使用 Spark Streaming 解决

总结 Spark 场景有以下几个：

- Spark 是基于内存的迭代计算框架，适用于需要多次操作特定数据集的应用场合。**需要**
  **反复操作的次数越多，所需读取的数据量越大，受益越大**，数据量小但是计算密集度较大
  的场合，受益就相对较小

- 由于 RDD 的特性，Spark 不适用那种异步细粒度更新状态的应用，例如 web 服务的存
  储或者是增量的 web 爬虫和索引。就是**对于那种增量修改的应用模型不适合**

- **数据量不是特别大，但是要求实时统计分析需求**


行业应用场景：

1. Yahoo 将 Spark 用在 Audience Expansion 中的应用，进行**点击预测和即席查询**等
2. 淘宝技术团队使用了 Spark 来解决多次迭代的**机器学习算法、高计算复杂度的算法**等。
   应用于**内容推荐、社区发现**等
3. 腾讯大数据精准推荐借助 Spark 快速迭代的优势，实现了在“**数据实时采集、算法实**
   **时训练、系统实时预测**”的全流程实时并行高维算法，最终成功应用于广点通 PCTR 投放
   系统上。
4. 优酷土豆将 Spark 应用于**视频推荐(图计算)、广告业务**，主要实现**机器学习、图计算**等
   迭代计算。



# 安装

> 三大主要版本：
> Spark-0.X
> Spark-1.X（主要 Spark-1.3 和 Spark-1.6）
> Spark-2.X（最新 Spark-2.3）
> 官网首页：http://spark.apache.org/downloads.html
>
> Spark-2.3 需要依赖：Java 8+ 和 Python 2.7+/3.4+ 和 Scala 2.11 和 R 3.1+
>

1. 上传解压,添加环境变量
2. 修改配置文件
   - spark-env.sh
   - slaves
   - spark-default.conf
3. 发送到其他节点

## 配置高可用

- 修改sparK-env.sh

- ```
  export SPARK_DAEMON_JAVA_OPTS="-Dspark.deploy.recoveryMode=ZOOKEEPER -Dspark.deploy.zookeeper.ur
  l=bd1:2181,bd2:2181,bd3:2181 -Dspark.deploy.zookeeper.dir=/spark"
  ```






# Spark相关的概念

## Master

> 是spark集群的管理者,或者是管理节点,在Spark基于yarn的方式运行过程中,master就是ResourceManager,基于自身的standalone模式运行就是集群中查看的master节点
>
> **作用:就是整体的资源调度**
>
>

## Worker

> 是spark集群中的工作节点,在spark基于yarn的方式运行过程中,workder就是nodemanager,基于自身的standalone模式运行就是集群中查看的Worker节点
>
> **作用:**本台机器上面的资源调度

## RDD

> 弹性式分布式数据集:能够被并行计算的,被分区存储在集群中的各个几点的数据集
>
> 存储的数据在rdd的对应的各个partition分区中
>
> 弹性:spark的数据在内存中进行存储或计算,当内存存储不了的时候,会将对应的数据外溢到磁盘中进行存储和计算

> rdd的创建方式:
>
> 1. 可以hdfs文件创建
> 2. 还可以通过并行的scala的集合创建
> 3. 在编码过程中还可以通过转化,将一个rdd转化为其他的rdd

## Application

> 提交的spark集群中去运行的完整的应用就是一个application ,当然,一个application由多个job组成

## Job

> 每提交的集群一次，就会说触发一次action操作，就是提交一次spark作业job,从这个角度而言，一个application由多个job组成。
>
> 可以理解为MapReduce中的job

## SparkContext

> 整个application的应用的上下文对象
>
> 一个spark应用的入口就是sparkContext,是Spark作业中最重要的概念
>
> Driver+ job就是Application

## Driver

> 驱动,简单理解,就是用来提交代码,提交job作业
>
> job在集群中运行,而这些driver是在本地提交这些job作业的
>
> Driver的部分就是在一 个application中除去job的部分都是Driver部分



# Spark Rdd的计算过程

1. 加载数据,从hdfs(hdfs://bd1807/data/hello.txt)中加载文件到内存中

2. 将数据加载到不同的分区,每个节点有部分数据

3. 将数据进行flatMap之后会生成一个新的rdd

   **数据不会保留,假设原始数据是1G,新的rdd数据也是1G,内存不断的增长**

4. 继续执行操作,转换成新的rdd(key,1)

5. 执行reducebyKey ,不同partition的数据会分发到一个新paratiton.(聚合统计)



# 项目创建

1. 创建一个sparkConfig对象,new SparkConfig(true),默认加载默认的配置信息
2. 创建一个**SparkContext对象**,加载关联的sparkConf对象,加载**外部数据源**,产生rdd
   1. java的api多一个java---JavaSparkContext
   2. 在一个spark中,只能有一个active的SparkContext
   3. jsc.textFile(path): 加载外部的普通的文本文件
3. 对rdd进行各种操作
4. 关闭sparkcontext,释放资源

## 例子

```java
package core.p1;

import org.apache.spark.SparkConf;
import org.apache.spark.api.java.JavaPairRDD;
import org.apache.spark.api.java.JavaRDD;
import org.apache.spark.api.java.JavaSparkContext;
import org.apache.spark.api.java.function.*;
import scala.Tuple2;

import java.lang.reflect.Array;
import java.lang.reflect.Parameter;
import java.util.Arrays;
import java.util.Iterator;

public class _01WordCount7 {
    public static void main(String[] args) {
        SparkConf conf = new SparkConf();
        conf.setMaster("local[2]");
        conf.setAppName(_01WordCount7.class.getSimpleName());
        JavaSparkContext jsc = new JavaSparkContext(conf);
        JavaRDD<String> linerdd = jsc.textFile("d:/data/hello.txt");
//        linerdd.foreach(new VoidFunction<String>() {
//            @Override
//            public void call(String s) throws Exception {
//                System.out.println(s);
//            }
//        });

        //这些XXXXFunction中的参数，由二个部分，
        // 第一部分为输入，参考函数的调用者
        // 第二部分为输出，看我们需要什么类型
        JavaRDD<String> wordRDD = linerdd.flatMap(new FlatMapFunction<String, String>() {
            @Override
            public Iterator<String> call(String s) {
                //\s空白符号(制表符,回车等)
                // + 一个或多个
                // * 匹配0 次huo多个
                // ? 一次或0次
                String[] temp = s.split("\\s+");
                return Arrays.asList(temp).iterator();
            }
        });

//        wordRDD.foreach(new VoidFunction<String>() {
//            @Override
//            public void call(String s) throws Exception {
//                System.out.println(s);
//            }
//        });

        //转为map(word,1)
        JavaPairRDD<String, Integer> pairRDD = wordRDD.mapToPair(new PairFunction<String, String, Integer>() {

            @Override
            public Tuple2<String, Integer> call(String s) throws Exception {
                return new Tuple2<>(s, 1);
            }
        });

//        pairRDD.foreach(new VoidFunction<Tuple2<String, Integer>>() {
//            @Override
//            public void call(Tuple2<String, Integer> pair) throws Exception {
//                System.out.println(pair._1+"\t"+pair._2);
//            }
//        });

        //进行数据统计
        JavaPairRDD<String, Integer> resultRDD = pairRDD.reduceByKey(new Function2<Integer, Integer, Integer>() {
            @Override
            public Integer call(Integer integer, Integer integer2) throws Exception {
//                System.out.println(integer+"====================>"+integer2);
                return integer + integer2;
            }
        });

        //结果展示
        resultRDD.foreach(new VoidFunction<Tuple2<String, Integer>>() {
            @Override
            public void call(Tuple2<String, Integer> tuple2) throws Exception {
                System.out.println(tuple2._1+"------"+tuple2._2);
            }
        });

        //释放资源
        jsc.close();
    }
}

```



## 错误

```java
SparkConf conf = new SparkConf();

org.apache.spark.SparkException: A master URL must be set in your configuration
原因:在构建一个spark作业的时候,一定要指定一个spark作业的运行方式
conf.setMaster("local[2]");

An application name must be set in your configuration
必须要为设置一个应用名称
名字随便.一般取类名
conf.setAppName(_01WordCount7.class.getSimpleName());
```

## spark的运行方式

1. local:在本地运行,在本地创建SparkContext对象

   - `local`:给当前Spark作业只分配一个cpu core ,一个线程运行,并行度是1
   - `local[N]:给当前Spark作业只分配N个cpu core ,一个线程运行,并行度是N`
   - `local[*]:根据当前机器,自动分配线程个数`
   - `local[N,R]`:比上述多了一个允许失败的次数,R次

2. standlone:提交到集群运行,就是master运行,并**行度需要在spark-submit脚本中进行设置**

   > `spark://bigdata01:7070`

3. yarn:**基于yarn的方式运行spark作业**

   - `yarn-cluster`:

     **sparkContext的创建在yarn集群中**

   - `yarn-client`:

     **sprakContext的创建在本地**

   > 测试环境： 一般使用yarn-client
   >
   > 生产环境： 使用yarn-cluster

4. mesos(略)

   - `mesos-cluster`
   - `mesos-client`



## 基于lambda的WordCount

> 需要jdk1.8

```java
package core.p1;

import org.apache.spark.SparkConf;
import org.apache.spark.api.java.JavaPairRDD;
import org.apache.spark.api.java.JavaRDD;
import org.apache.spark.api.java.JavaSparkContext;
import org.apache.spark.api.java.function.FlatMapFunction;
import org.apache.spark.sql.SparkSession;
import scala.Tuple2;

import java.sql.SQLOutput;
import java.util.Arrays;

public class _02WordCountLambada {
    public static void main(String[] args) {
        SparkConf conf = new SparkConf().setAppName(_02WordCountLambada.class.getSimpleName())
                .setMaster("local[2]");
        JavaSparkContext jsc = new JavaSparkContext(conf);

        JavaRDD<String> linerdd = jsc.textFile("d:/data/hello.txt");
        JavaRDD<String> wordRDD = linerdd.flatMap((line) -> {
            String[] fields = line.split("\\s+");
            return Arrays.asList(fields).iterator();
        });

        JavaPairRDD<String, Integer> pairRDD = wordRDD.mapToPair(word -> new Tuple2<String, Integer>(word, 1));

        JavaPairRDD<String, Integer> resultRDD = pairRDD.reduceByKey((v1, v2) -> v1 + v2);

        resultRDD.foreach(s -> System.out.println(s));

        jsc.close();

    }
}

```



## Scala版本的SparkWordCount

> SparkContext创建
>
> 可能出现的错误,访问的时候要将hdfs-site.xml,core-site.xml放到resources目录下

```scala
package com.aura.core

import org.apache.spark.{SparkConf, SparkContext}

object _01ScalaSpark {
  def main(args: Array[String]): Unit = {
    val conf = new SparkConf()
      .setAppName(s"${_01ScalaSpark.getClass.getSimpleName}")
      .setMaster("local[2]")
    val sc = new SparkContext(conf)
    val lineRDD = sc.textFile("/hive.txt")
    val wordsRDD = lineRDD.flatMap(line=>line.split("\\s+"))
    val pairRDD = wordsRDD.map(word => (word,1))
    val resultRDD = pairRDD.reduceByKey((v1,v2)=> v1+v2)
    resultRDD.foreach(r=>println(r._1+"==>"+r._2))

    //释放资源
    sc.stop()
  }
}

```

## Scala版本的SparkWordCount

> SparkSession创建形式

```scala
package com.aura.core

import org.apache.spark.sql.SparkSession
import org.apache.spark.{SparkConf, SparkContext}

object _02ScalaSpark {
  def main(args: Array[String]): Unit = {
    val spark = SparkSession.builder()
      .appName(s"${_02ScalaSpark.getClass.getSimpleName}")
      .master("local")
      .getOrCreate()
    val sc = spark.sparkContext

    val lineRDD = sc.textFile("hdfs://bd1807/hive.txt")
    val wordsRDD = lineRDD.flatMap(_.split("\\s+"))
        .map((_,1)).reduceByKey(_+_).foreach(println)

    //关闭
    spark.stop()
  }
}

```

# spark作业提交到集群



1. 将之前的代码打成jar包

   > 去掉master,将路径改为手动

   ```scala
   package com.aura.core

   import org.apache.spark.sql.SparkSession

   object _03SparkWordCountUpload {
     def main(args: Array[String]): Unit = {
       if(args==null || args.length<1){
         println(
           """
             |Paramenter Error! Usage:<inputPath>
             |inputPath: 程序数据输入源
           """.stripMargin)
         System.exit(-1)
       }

       var Array(inputPath)=args

       val spark = SparkSession.builder()
         .appName(s"${_02ScalaSpark.getClass.getSimpleName}")
         .getOrCreate()
       val sc = spark.sparkContext

       val lineRDD = sc.textFile(inputPath)
       val wordsRDD = lineRDD.flatMap(_.split("\\s+"))
         .map((_,1)).reduceByKey(_+_)
         //.collect()//将集群中的rdd对应的partition中的数据,拉取到dirver中,在工作中慎用
         .foreach(println)


       //关闭
       spark.stop()
     }
   }

   ```

2. 上传到虚拟机中

3. 编写脚本

   ```scala
   export HADOOP_CONF_DIR=/home/sun/apps/hadoop-2.7.6/etc/hadoop
   /home/sun/apps/spark-2.2.2-bin-hadoop2.7/bin/spark-submit \
   --class com.aura.core._03SparkWordCountUpload \
   --master spark://hdp01:7077 \
   --deploy-mode client \
   --executor-memory 600M \
   --num-executors 1 \
   spark-wc.jar \
   hdfs://bd1807/hive.txt
   ```



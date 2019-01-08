---
title: hbase
tags:
  - null
  - null
date: 2018-10-31 09:27:04
categories:
top:
---

- sql: 结构化查询语言,针对于二维表格的一种脚本语言
- Nosqll: 这个类型的数据的数据存储格式:key-value

> 用一句话总结: 能使用sql语法进行增删改查的数据库就是sql数据库,否则就是nosql数据库

所谓的nosql数据库就是一个很大很大的map



Nosql:No sql

NoSQL: Not only SQL(基于各种nosql数据库都有sql客户端)

hbase: put delete put get scan

sql客户端: 用户编写的sql 这个工具帮助把这个sql转换成hbase认识的命令

# hadoop是什么东西

> 平台 
>
> hdfs 	mapreducea	yarn
>
> ntfs qq eclispse chrome  windows(操作系统:桥梁 把软件所需要做的硬件资源进行管理 )



> 一个完整的计算机:
>
>  - 操作系统
>  - 文件系统
>  - 提供一个计算模型 编写软件运行计算任务

> 一个完整的计算集群:----> 一台强大的服务器
>
> 功能: 把多个服务器组合在一起,形成一个整体
>
> 对于hadoop用户,不需要了解hadoop到底由多少台服务组成
>
> 我们只需要认为我们使用的hdfs 或者yarn等等都是一台服务器



# HBase:

```
HBase  是一个 通过大量廉价 机器 解决海量数据的高速存储和读取的分布式数据库解决方案

hbase其实就是一个大集群,内部由
```



```
mysql

hbase
四维标（立体空间 +时间）
相当于是二维表查出的结果是一个二维表
student.get(rowkey,family,colume,timestampe)

列簇：包含的key的个数可以不一致，可以不确定，可以随机指定

student.get(rowkey,family,colume)=默认最新的值
student.get(rowkey,family)=是这个rowkey,family的key-value

student.get(rowkey,family,colume//key,timestampe//value)

```

##  HBase  这个 NoSQL  数据库的要点 ：
①  高并发，以扩 展，解决海量数据集的随机实时增删改查
② HBase  本质依然是 Key-Value  数据库， 查询数据功能很简单，**不支持 join  等复杂操作**( 可通过 Hive  支持来实现多表 join  等复杂操作)
③  不支持复杂的事务，**只支持行级事务**
④ HBase  中支持的数据类型：byte[] （底层所有数据的存储都是字节数组）
⑤ 主要用来存储结构化和半结构化的松散数据。】

<!-- more -->

## HBase  中的表 特点
1 、 大 ：一个表可以有上十亿行，上百万列
2 、 面向列 ： 列可以灵活指定， 面向列( 族) 的存储和权限控制，列( 簇) 独立检索。
3 、 稀疏 ：对于为空(null) 的列，并不占用存储空间，因此，表可以设计的非常稀疏。
4 、 无 严格 模式 ：每行都有一个可排序的主键和任意多的列，列可以根据需要动态的增加，
同一张表中不同的行可以有截然不同的列



## 关于存储系统的模式介绍：
读模式：输入写入的时候不做检验，在读取数据的时候做模式校验，比如数据仓库 Hive
写模式：数据库都是写模式的，在写入数据进入存储系统的时候做模式校验，比如 RDBMS



##  行键 （RowKey ）

> 1. 与 NoSQL 数据库们一样，RowKey 是用来检索记录的主键。访问 HBase Table 中的行。
>
> 2. rowKey 行键可以是任意字符串(最大长度是 64KB，实际应用中长度一般为 10-100bytes)，**最**
>    **好是 16。**(64位机器是8字节)在 HBase 内部，RowKey 保存为字节数组。
>
> HBase  会对表中的数据按照 rowkey 排序(字典排序）

> 存储时，数据按照 RowKey 的字典序(byte order)排序存储。设计 Key 时，要充分排序存储这
> 个特性，将经常一起读取的行存储放到一起。(位置相关性)

三种方式：
1 、 通过单个 row key  访问
2 、 通过 row key 的 的 range
3 、全表扫描





##  列簇（Column Family）

> HBase 表中的每个列，都归属与某个列簇。
>
>  列 簇的 是表的 Schema  的一部分( 而列不是)，
>
> 必须在创建表的时候指定。指定好了列簇就不能更改。
>
> 列簇可以增加或者删除，删除的时候会删除这个列簇中的所有数据



> **列名都以列簇作为前缀**。例如 courses:history，courses:math 都属于 courses 这个列簇。
> 访问控制、磁盘和内存的使用统计等都是在列簇层面进行的。

> 列簇越多，在取一行数据时所要参与 IO、搜寻的文件就越多，所以，如果没有必要，不要
> 设置太多的列簇，官网推荐是小于等于 3（最好就一个列簇）

##  时 间戳 （TimeStamp ）

> HBase 中通过 RowKey 和 Column 确定的为一个存储单元称为 Cell。
>
> 每个 Cell 都保存着同一份数据的多个版本。
>
> 版本通过时间戳来索引
>
> 时间戳的类型是 64 位整型。时间戳可以由 HBase(在数据写入时自动)赋值，此时时间戳是精确到毫秒的当前系统时间。
>
> 时间戳也可以由客户显式赋值。

> 每个
> Cell 中，不同版本的数据按照时间倒序排序，即最新的数据排在最前面。HBase 在查询的时候，**默认返回最新版本/最近的数据**。如果需要读取旧版本的数据，可以指定时间戳

**数据版本回收方式：**为了避免数据存在过多版本造成的的管理(包括存储和索引)负担

1. 保存数据的最后 n  个版本

2. 保存最近一段时间内的版本（设置数据的生命周期 TTL)

   用户可以针对每个列簇进行设置。

##  单元格（Cell ）

> 由`{RowKey, Column( =<Column Family> + <Qualifier>), Version}` 唯一确定的单元。
>
> Cell 中的数据是没有类型的，全部是**字节码形式存储**



## HBase   应用场景

1. **半结 构化或非结构化数据**
   对于数据结构字段不够确定或杂乱无章很难按一个概念去进行抽取的数据适合用 HBase。而
   且 HBase 是面向列的，HBase 支持动态增加字段
2. **记录非常稀疏**
   RDBMS 的行有多少列是固定的，为 null 的列浪费了存储空间。而 HBase 为 null 的 Column
   是不会被存储的，这样既节省了空间又提高了读性能。
3. **多版本数据**
   对于需要存储变动历史记录的数据，使用 HBase 就再合适不过了。HBase 根据 Row key 和
   Column key 定位到的 Value 可以有任意数量的版本值。
4. **超大数据 量 的随机、实时读写**
   当数据量越来越大，RDBMS 数据库撑不住了，就出现了读写分离策略，通过一个 Master 专
   门负责写操作，多个 Slave 负责读操作，服务器成本倍增。随着压力增加，Master 撑不住了，
   这时就要分库了，把关联不大的数据分开部署，一些 join 查询不能用了，需要借助中间层。
   随着数据量的进一步增加，一个表的记录越来越大，查询就变得很慢，于是又得搞分表，比
   如按 ID 取模分成多个表以减少单个表的记录数。经历过这些事的人都知道过程是多么的折
   腾。采用 HBase 就简单了，只需要加机器即可，HBase 会自动水平切分扩展，跟 Hadoop 的
   无缝集成保障了其数据可靠性（HDFS）和海量数据分析的高性能（MapReduce）。
5. **查询简单**
   不涉及到复杂的 Join 查询，基于 RowKey 或者 RowKey 的范围查询

# hbase 集群结构

- Master 
- HRegionServer

> 如果Master宕机 整个Hbase依然可用,
>
> master是功能上的主节点。不是数据状态的主节点
>
> 如果master宕机，部分功能不能用：增删改表的定义
>
> 但是还是可以使用其他一些功能：比如查询

> HDFS--->File--->block----->namenode(metadata)
>
> Hbase--->table--->Region----->zookeeper(metadata)

> hbase的关于元数据的操作，都是必须经过master,也就是说只有master才有权限去更改元数据
>
> maser和namenode master 的压力要小得多，并且甚至宕机一段时间都没有问题
>
> master节点不能长时间宕机，比如宕机一个小时都没有问题
>
> 关于region的负载均衡是有master来做的

## Region

> 是负载均衡的最小单元
>
> region的大小：0.x 1G   // 1.x 10G 刚刚均匀分 5g 5g

是 HBase 将一个表中的所有数据按照 RowKey 的不同范围进行切割的逻辑单元，每
个Region负责一定范围数据的读写访问。Region由RegionServer负责管理。HBase中的Region
的概念就和 HDFS 中的数据块的概念差不多，Region 是 HBase 表切分出来的一个分片。数据
块是 HDFS 中的一个大文件切分出来的一个分片。

## HMaster

HBase 的主节点，负责整个集群的状态感知、负载分配、负责用户表的元数据
(schema)管理（可以配置多个用来实现 HA），HMaster 负载压力相对于 HDFS 的 NameNode
会小很多。HBase 的 HMaster 其实就算是宕机一段时间也可以正常对外提供服务的(要搞清
楚为什么)。

## RegionServer

HBase 中真正负责管理 Region 的服务器，也就是负责为客户端进行表数据读
写的服务器。每一台 RegionServer 会管理很多的 Region，一个 RegionServer 上面管理的所有
的region不属于同一张表。负责Region的拆分，负责和底层的HDFS的存储交互，负责StoreFile
的合并。

## ZooKeeper

整个 HBase 中的主从节点协调，元数据的入口，主节点之间的选举，集群节点
之间的上下线感知……都是通过 ZooKeeper 来实现

## HDFS

用来存储 HBase 的系统文件，或者表的 Region 文件

## Client

Client 包含了访问 HBase 的接口，另外 Client 还**维护了对应的 Cache 来加速 HBase 的**
**访问**，比如 Cache 的.META.元数据的信息。



## HBase 和 和 Hive  的 比较

- OLAP(hive):数据库  analusis	select
- OLTP(hbase): 数据库  transaction insert updeate delete
- hbase进行select  ,hive整合hbase

## 相同点

1、HBase 和 Hive 都是架构在 Hadoop 之上，用 HDFS 做底层的数据存储，用 MapReduce 做
数据计算

## 不同点
1、	Hive 是建立在 Hadoop 之上为了**降低 MapReduce 编程复杂度**的 ETL 工具。
​	HBase 是为了**弥补 Hadoop 对实时操作的缺陷**
2、	Hive 表是**纯逻辑表**，因为 Hive 的本身并不能做数据存储和计算，而是完全依赖 Hadoop
​	HBase 是**物理表**，提供了一张超大的内存 **Hash 表来存储索引，方便查询**
3、	Hive 是**数据仓库工具**，需要**全表扫描**，就用 Hive，因为 Hive 是**文件存储**
​	HBase 是**数据库**，需要**索引访问**，则用 HBase，因为 HBase 是**面向列的 NoSQL 数据库**
4、	Hive 表中存入数据（文件）时不做校验，属于**读模式**存储系统
​	HBase 表插入数据时，会和 RDBMS 一样做 Schema 校验，所以属于**写模式**(无严格模式)存储系统
5、	Hive **不支持单行记录**操作，数据处理依靠 MapReduce，操作**延时高**
​	HBase **支持单行记录的 CRUD**，并且是**实时处理**，效率比 Hive 高得多



# 搭建


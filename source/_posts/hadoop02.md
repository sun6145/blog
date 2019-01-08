---
title: hadoop02
tags:
  - null
  - null
date: 2018-09-29 09:05:25
categories:
top:
---



1. 安装过程中的问题
2. Hadoop的安装模式
3. hdfs 的设计思想
4. hdfs的主从架构
5. hdfs的优缺点
6. hafs常用操作

<!-- more -->

谷歌的三篇论文  用java实现

google 2003 --------  GFS  Mapreduce  bigtable

doung cutting -----------java

GFS   -------------hdfs

 Mapreduce  ------------Mapreduce 

bigtable ---------hbase



# hadoop是什么

高可用 高可靠的分布式开源框架 基于普通廉价机

# hadoop的模块

hadoop1.0 

- hdfs
- mapreduce

hadoop2.0

- hdfs :主从 一主多从
  - 主: namenode
  - 从: datanode
  - 助理:secondarynamenode
- mapreduce
- yarn
  - 主从架构
  - 主:resourcemanager
  - 从: nodemanager
- hadoop的安装
  1. 伪分布式
  2. 完全分布式

# 安装过程中的问题

## 某些进程启动失败

>  启动集群的过程中,发现某些进程启动不了,集群运行一段时间后某个进程挂了?

**启动时候的日志文件**:$HADOOP_HOME/logs

```
Starting namenodes on [hdp01]
hdp01: starting namenode, logging to /home/sun/apps/hadoop-2.7.6/logs/hadoop-sun-namenode-hdp01.out
hdp02: starting datanode, logging to /home/sun/apps/hadoop-2.7.6/logs/hadoop-sun-datanode-hdp02.out
hdp03: starting datanode, logging to /home/sun/apps/hadoop-2.7.6/logs/hadoop-sun-datanode-hdp03.out
Starting secondary namenodes [hdp02]
hdp02: starting secondarynamenode, logging to /home/sun/apps/hadoop-2.7.6/logs/hadoop-sun-secondarynamenode-hdp02.out
```

1. **确保集群没有问题:查看集群启动的日志文件**

   **hadoop-sun-namenode-hdp01.log**

   ```
   日志文件的命名规则:
   hdfs的相关进程  ---hadoop模块
   yarn的相关简称   ----yarn 模块
   进程的归属模块  用户名  进程名字  主机名.log
   ```

2. 对于问题,查看日志文件

   ```
   哪一个进程没有,查看哪一个进程的日志文件
   查看日志文件的命令  tail -100 日志文件
   如果报错: 同java代码报错
   
   
   如果日志文件不报错: 集群没有问题
   	有报错信息: 需要先解决报错
   没有报错  进程不启动
   	1. 停掉集群 全部重启  暴力
           hdfs的相关进程
               stop-dfs.sh
               start-dfs.sh
           yarn的相关进程
               stop-yarn.sh
               start-yarn.sh
       2. 单独启动没有启动的进程
       	hdfs
       		hadoop-daemon.sh 单独启动,某一个节点上的某一个hdfs进程
       		hadoop-daemons.sh 启动多个节点上的某一个hdfs进程
       		
       		hadoop-daemon.sh start namenode
       		hadoop-daemon.sh start datanode
       		hadoop-daemon.sh start secondarynamenode
           yarn
           	yarn-daemon.sh 单独启动,某一个节点上的某一个yarn进程
       		yarn-daemon.sh start resoucemanager
       		yarn-daemon.sh start nodemanager
   ```







##  格式化的问题

> **ps -ef | grep java | grep hadoop:**查看hadoop的进程

   > 成功格式化只能格式化一次

   原因: 

   > 集群安装的过程中 配置了namenode的数据存储目录

   ```
   <property>
   <name>dfs.namenode.name.dir</name>
   <value>/home/hadoop/data/hadoopdata/name</value>
   <description>namenode的数据的存储目录</description>
   </property>
   ```

**namenode存储目录有二个内容**

1.  current

```
current 存储的是namenode 的数据信息
	namenode存储的数据 就是datanode上的数据的描述信息
	描述数据的数据--------元数据
	
--------------------------------------------------


current下有一个核心的文件 VERSION 版本信息文件
   格式化的时候生成
   
#Sat Sep 29 15:54:29 CST 2018
namespaceID=2080811177
clusterID=CID-4a07165e-6c8b-47bb-afc8-e8f473394a38D   //集群的 集群版本ID 
cTime=0
storageType=NAME_NODE	//	存储类型
blockpoolID=BP-1194953031-192.168.2.101-1538040436190	//块池id
layoutVersion=-63
   
clusterID:集群的 集群版本ID 集群的唯一标识
datanode和namenode相互通信的根基,
datanode里面也会存储这个信息   
    如果两个版本信息一致  
        则会认为是同一个集群   
    否则 
        认为是不同的集群
	
```

2. in_use.lock

   ```
in_use.lock:
	锁文件:就是保证一个节点只启动一个namenode进程
   ```

3.datanode的version:**datanode启动的时候生成的**

> 路径：/home/hadoop/data/hadoopdata/data/current

   ```
#Sat Sep 29 16:07:15 CST 2018
storageID=DS-e390bfb6-9b44-4cc4-a22e-a9259b6f4935
clusterID=CID-4a07165e-6c8b-47bb-afc8-e8f473394a38		集群id   集群的标志
cTime=0
datanodeUuid=22c87409-cd54-42b6-b34a-63bcc935c3e5
storageType=DATA_NODE
layoutVersion=-56
   
   ```

   > **格式化的时候:只生成了namenode的version信息 没有生成datanode的版本信息**
   >
   > datanode的version启动datanode的时候生成
   >
   > **重新格式化会造成namenode的version被覆盖 datanode的version不会被覆盖**
   >
   > ​	
   >
   > 格式化了二次:集群肯定是关闭
   >
   > ​	**将所有的节点的数据目录删除,再创新格式化**
   >
   > ​	**再生产中要谨慎使用 容易造成数据丢失**



## 时间同步

在集群的安装过程中 需要保证 各个节点之间的时间同步

> 原因: datanode和namenode之间需要通信的
>
> 只要各个节点之间的时间一致就可以 ,不是节点时间和北京时间一致

实现

- 手动 date -s 时间
- 时间同步服务器实现
  - 手动搭建
  - 用公网的
    - ntpdate 时间同步服务器的公网ip
    - sudo ntpdate ntp1.aliyun.com

## 配置过程中的环境变量

配置过程中的环境变量

1. 系统的环境变量位置:/etc/profile ----所有用户都生效的
2. 用户环境变量  : ~/.bashrc  -----只针对当前用户有效
3. 用户环境变量 : ~/.bash_profile  -----只针对当前用户有效 

加载顺序:

1. 系统环境变量:/etc/profile
2. 用户环境下的:~/.bashrc
3. 用户环境变量:~/.bash_profile

> **生效的顺序与加载顺序相反,最后加载最先生效**







# Hadoop的安装模式

## 单机模式

> 不需要安装,直接解压就可以用
>
> 生产中不会使用
>
> 不存在分布式文件系统 没有守护进程,只在一个节点上运行
>
> 生产中不会使用 个人学习的时候 代码调试

## 伪分布式

> 存在分布式文件系统 有守护进程
>
> 只不过所有的进程只在一个节点上运行
>
> 生产中不用 个人测试的时候

## 完全分布式

> 存在所有的分布式文件系统 所有的进程运行在不同的节点的
>
> 运行在多个节点之上 多个节点共同组成的一个集群
>
> 运行模式: 一主多从一助理
>
> 文件系统:
>
> ​	一个namenode
>
> ​	多个datanode
>
> ​	一个secondarynamenode: 是namenode的冷备份,只复制数据,不能当做namenodenamenode
>
> yarn: 
>
> ​	一主多从
>
> ​	极少的企业 小型的公司 测试集群
>
> **缺陷:**存在单点故障的问题,主节点自由一个 secondarynamenode是仅仅备份namenode的元数据 不会主动切换为namenode
>
> 当namenode宕机的时候 会造成集群整体不可用 数据不可访问

## 高可用(ha   high avalible)

> 解决完全分布式中的单点故障问题(hdfs的单点故障)
>
> 运行模式:
>
> ​	多个主节点 多个从节点 一般 2个主节点 多个从节点
>
> 2个主节点,同一时间 只能允许一个对外提供服务,另外一个处于热备份状态(时刻准备接替为active namenode)
>
> 对外提供服务的: **active namenode**
>
> 热备份的:**standby namenode**
>
> 这种模式中 多个namenode之间的数据肯定是实时同步的 存储的数据是一致的,如果active namenode 宕机的时候 standby namnode才可以接替 数据不丢失
>
> 实际生产中 对于绝大多数公司足够用 集群规模 中小规模 大规模也可以 不要太大规模 100台左右

**缺陷**

虽然这种模式有二个namenode 同一时间对外提供访问的自由一个,如果集群中的datanode的节点个数很多的时候 会造成namenode的压力过大

## 联邦模式

> 解决集群过大 ,namenode压力过大的问题
>
> **同一时间同时对外提供服务的namenode有多个 active namenode**
>
> 每个namenode 只负责集群中的一部分的数据管理(不是datanode的管理)

如何区分哪一个namenode管理的哪一个部分数据:

一个datanode下有多个块池

> namemode的version中记录
>
> blockpooID:块池ID,记录自己需要管理的数据块的信息
>
> namenode管理数据块的标志
>
> 不同的namenode管理的数据的blockPoolID不一样
>
> 当集群中的节点个数过多的时候可以真正的分担namenode的数据管理的压力

注意:

​	区分每一个namenode管理的是datanode上的一部分数据

​	而不是(每一个namenode管理几个datanode)对个集群

> **生产环境: 超大集群 :联邦模式+高可用**

# hdfs的理论

hadoop2.0

1. hdfs:

   >  hadoop distibuted filesystem hadoop 的分布式文件系统
   >
   > 海量数据的分布式存储
   >
   > 来源谷歌的GFS论文

2. mapreduce:

   > 分布式计算的

3. yarn

   > 分布式资源调度

## hdfs 的设计思想

## 分块存储

```
块怎么分?
	数据块太大: 负载不均衡
	数据块太小: 每一个数据块会被namenode记录 namrnode记录的内容太多,namenode的压力过大

hdfs设计的时候已经设计了一个比较合理的值:
	hadoop1.0 64M
	hadoop2.0 128M 在hadoop的默认配置中 $HADOOP_HOME/share/hadoop/hdfs
	dfs.blocksize 134217728(128M)
	如果hdfs-site.xml中配置了这个参数 会把默认的hdfs-default.xml覆盖掉
	默认的大小 不会造成负载不均衡 同时不会造成namenode的压力过大
注意:
	进行数据存储的时候 假设300M
	block_1 128M
	block_2 128M
	block_3 44M
	最后的数据不足128 ,也单独存储一个块 不会和其他数据混合存储
```



## 备份存储  冗余存储

hadoop设计的时候 基于廉价机

如果每一个数据只存储一份 任意 一个节点宕机 会造成数据丢失

hdfs在进行设计时候 每一个数据都要进行备份存储 冗余存储 保证数据的安全性

副本:

> 相当于备份的概念 但是和备份不一样的一点 这里的备份没有主从之分 所有的备份数据 和原始数据同等地位 针对用户的读写时候 无差别 对外提供服务的时候 是根据哪一个副本空余 哪一个副本就对外提供服务 ;都空余的时候,就近原则



**hdfs的默认的备份存储的副本个数3份**

dfs.replication 2 我们自己配置的

如果我们不进行配置 这个参数 默认每一个数据块存储3份副本



### 副本的放置(用空间换数据安全)

保证数据的安全性:

同一个数据块的不同副本 放置在不同的节点上

但是同一个节点可以存放对个不同的数据块

同一个节点上 如果放置同一个数据块的二个副本  没有意义

同一个节点上 只能存储一个数据块的一个副本

3个副本

 	副本1   ---------------datanode01

​	副本2   ---------------datanode02

​	副本3   ---------------datanode03



**如果副本个数设置为3 有一个副本所在的节点宕机 如何处理?**

```
少于设置的个数: 这个时候hdfs会自己再复制一个副本出来 最终保证3个副本

如果副本复制完成了 刚才宕机的节点恢复了 这个时候 副本个数就变成4个 
大于设置的副本个数,如何处理?
namenode 会经过1h左右的时间如果发现还是4个副本(集群稳定) hdfs会删除一个副本 保证副本个数为3

```

**如果集群中datanode的节点个数 2 个 设置的副本的个数为3**

```
这时候集群中只能保存节点个数的副本
每个节点保存一个副本 最终只有二个副本
剩下的一个副本会进行记账 当集群中的节点增加的时候会把这个副本补上

```



# hdfs的架构

## hdfs的简单使用

```
hdfs的上传存储的命令
上传   本地  ---------- hdfs集群中
这里的本地   客户端所在的本地  ----> hdfs集群中
	客户端	-----本地
	linux ----linux
	eclipse ---- windows

hadoop fs -put 本地的文件 hdfs 的路径
	本地文件 可以是绝对路径 也可以说相对路径
	hdfs: 
		文件的目录结构 同linux
		只有绝对路径进行访问 没有相对路径的访问方式 所有的访问从/开始
		
	hadoop :   启动一个hadoop 客户端
	fs  file System   :打开hdfs的文件系统
	- 后面的是命令
	
查看目录结构:
hadoop fs -ls [-h] 目录 
	
```

# 主从架构

一主多从

```
主:namenode
1. 存储元数据信息
	元数据:管理datanode数据的数据
	包括3个部分
	
	1)抽象目录树
		对于hdfs来说  目录树不代表任何一个节点存储目录结构
        代表的是集群中的所有节点共同构成的存储集群的目录结构
        目录树叫抽象目录树
     
	2)数据和块的对应关系
		如果一个数据被切分成了多个块  这多个块之间的blockid一定是顺序递增的
		文件存储的时候 按照块为单位进行存储
		一个文件如果超过128M就会被切分成很多块
		假设 文件名 hadoop.gz 300M 切成3块
		hdfs在进行数据块的切分的时候每一个数据块都有一个数据块的编号
		这个数据块的编号 blockid 全局唯一
		/hadoop.gz
			blk_00000001
			blk_00000002
			blk_00000003
		/hdfs-site.xml 1.4kb
			blk_00000004
	3)数据块的存储位置
		和副本的个数有关 每一个块3个副本
		/hadoop.gz
			blk_00000001	[hadoop01 hadoop02 hadoop03]
			blk_00000002	[hadoop04 hadoop02 hadoop03]
			blk_00000003	[hadoop01 hadoop02 hadoop04]
            
2. 处理客户端的读写请求
	客户端读写 先去找namenode
	
	
```



```
从:datanode
1. 负责数据的真正存储
	数据块的存储 datanode负责
	hdfs-site.xml
        <property>
            <name>dfs.datanode.data.dir</name>
            <value>/home/hadoop/data/hadoopdata/data</value>
            <description>datanode 的数据存储目录</description>
        </property>
	datanode的所有数据信息  (相关信息数据 和 原始数据的数据块存储位置)
	
	
	
	
	/home/hadoop/data/hadoopdata/data/current/BP-1261971397-192.168.191.201-1538186485641/current/finalized/subdir0/subdir0
	
	1. BP-1261971397-192.168.191.201-1538186485641:块池文件
		块池文件 : 存储当前节点的所有的块的文件
	2. 目录下有文件
		blk_XXXXXXXXXX 实际的原始数据的块(文件)
		blk_XXXXXXXXXX.meta 上面的块的描述
	
2. 负责处理客户端真正的读写请求
	
```

```
助理:secondarynamenode
这里是一个namenode的冷备份的节点

	1) 帮助namenode备份元数据 放置namenode宕机的时候可以进行数据恢复
	
	2) 帮助namenode做一些事情 (元数据合并) 减轻namenode的压力 
```





# hdfs的优缺点

## 优点

1. 成本低   构建在廉价机器上
2. 高容错
3. 适合批处理(离线处理)
   1. 离线处理: 不适合处理实时数据
   2. 移动计算而非数据 数据位置暴露给计算框架,数据位置暴露给计算框架
   3. 移动计算:
      1. 计算在哪里 数据就在那里 数据跟着计算[跑]
      2. 浪费大量的数据在数据拷贝上
      3. 一台机器的计算能力有限
   4. 移动计算: 数据子那里 计算就到哪里 计算跟着数据[跑]
4. 适合大数据处理
   1. GB,TB,甚至PB级数据 百万规模以上的文件数据量 10K+节点规模
5. 流式文件访问
   1. 一次性写入 多次读取,保证数据一致性
   2. hdfs不支持文件修改
      1. 对于hdfs来说,文件修改的成本太高
      2. 但是支持文件追加 不建议使用 基本不用



## 缺点

1. 对于低延迟的数据访问不支持

   1. 对于实时的数据访问 不支持 只支持离线数据访问

2. 不适合小文件的存储

   1000w 1kb的文件

   > 原因: 
   >
   > 1. 每一个小文件都需要单独存储一个数据块,在进行文件访问的时候,先去访问namenode找到该数据块的位置 再到对应的datanode上进行文件读取 有可能找文件的过程(寻址)时间远远大于文件的真实读取时间
   >
   > 2. 会造成namenode 的压力过大
   >
   >    一个文件-------1个数据块--------1条元数据
   >
   >    很多数据块会对应很多条元数据 一条元数据150Byte
   >
   >    元数据大小
   >
   >    1000w*150byte =1 500 000 000 ≈1.5G
   >
   >    1000w*1kb=10000000=====10G   10T
   >
   >    会造成namenode的压力过大

3. 不支持数据修改  一次写入多次读取





# 常用命令操作

- 文件的上传

  >  `hadoop fs -put  文件   hdfs目录`:复制
  >
  > `hadoop fs -copyFromLocal 本地文件路径 hdfs的文件目录`
  >
  > `hadoop fs -moveFromLocal 本地文件路径 hdfs的文件目录`
  >
  > 注意:
  >
  > ​	在文件上传的过程中 如果没有指定文件名 以原来的文件名命名
  >
  > ​	如果指定文件名 则以指定的文件名命名   
  >
  > ​		/test   是重命名为test
  >
  > ​		/test/  放到test目录下(一定要存在)
  >
  > ​	hdfs不会自动创建上传文件的目录  上传文件的时候,**父目录一定要存在**

- 创建文件夹

  > `hadoop fs -mkdir /hdp`
  >
  > `hadoop fs -mkdir -p /hdp/aa/bb`:级联创建

- 文件下载

  > `hadoop fs -get hdfs文件目录   本地文件目录`:拷贝
  >
  > `hadoop fs -copyToLocal hdfs文件目录   本地文件目录`:拷贝
  >
  > `hadoop fs -moveToLocal 本地文件路径 hdfs的文件目录`:hdfs的文件下载完成 删除了
  >
  > hadoop fs -get /hdfs-site.xml ~/
  >
  > hadoop fs -copyToLocal /hdfs-site.xml ~/
  >
  > ```
  > [sun@hdp01 ~]$ hadoop fs -moveToLocal /hdfs-site.xml ~/aa 
  > moveToLocal: Option '-moveToLocal' is not implemented yet.(还没有实现)
  > ```

- 查看文件的目录信息

  > `hadoop fs -ls目录`:只能查看当前给定的目录下的目录树 不能级联查看
  >
  > `hadoop fs -lsr -R hdfs目录`:级联查看

- 删除文件

  > `hadoop fs -rm hdfs文件的绝对路径`

- 删除目录

  > `hadoop fs -rm -r -f hdfs目录`
  >
  > ```
  > [sun@hdp01 ~]$ hadoop fs -rm -r -f /hdp
  > 18/09/29 16:35:23 INFO fs.TrashPolicyDefault: Namenode trash configuration: Deletion interval = 0 minutes, Emptier interval = 0 minutes.
  > Deleted /hdp
  > ```

- 修改用户和组信息

  > linux:`chown [-R] 用户:组 文件 `
  >
  > hdfs修改
  >
  > ​	`hadoop fs -chown [-R] 用户:组 hdfs的路径`

- 修改文件的读写权限

  > linux :`chmod [-R] 文件读写权限 文件或目录`  
  >
  > 7(所属用户)5(所属组的用户)5(其他用户)
  >
  > 读:4 写2 执行1

  > hdfs
  >
  > `hadoop fs -chmod [-R] 文件读写权限 文件或目录 `

- 设置副本个数

  > 1. hdfs-default.xml: 默认个数3个
  >
  > 2. hdfs-site.xml:改为2
  >
  > 3. 通过命令设置副本个数
  >
  >    `hadoop fs -setrep [-R] [-w] 副本个数 目录`:命令只能修改指定路径的副本数
  >
  >    `[-R]` :级联
  >
  >    ​	`hadoop fs -setrep -R 副本个数 目录`:只能**修改**指定文件下**已经存在的文件的副本数**,新上传的按照hdfs-site.xml进行配置副本数
  >
  >    `[-w]`:wait,等待新设定的副本完成

- 查看文件内容

  > `[-cat]`
  >
  > ​	`cat fs -cat hdfs的文件目录`
  >
  > `[-tail]`:查看文件末尾1kb的数据
  >
  > ​	`hadoop fs -tail /a.sh`

- 新建一个空文件 (hdfs不能修改,不常用)

  > `hadoop fs -touchz hdfs目录/文件名`
  >
  > `hadoop fs -touchz /a.txt`

- hdfs文件的移动和复制

  > 移动:
  >
  > ​	`hadoop fs -mv hdfs路径 新路径 `
  >
  > 复制:
  >
  > ​	`hadoop fs -cp hdfs路径 新路径 `

- 其他

  ```
  - appendToFile 追加 追加到原始文件的末尾 成本高
  	hadoop fs -appendToFile 本地文件 hdfs目录
  	
  这里的追加是在原始的数据块上追加的 
  如果原始的数据块追加超过128M 这回切分
  
  ```

  ```
  合并下载:-getmerge
  将hdfs上的多个文件合并为一个文件下载到本地
  按照给的路径的顺序 进行合并下载
  hadoop fs -getmerge hdfs的多个文件路径(空格隔开) 本地文件路径
  ```

  ```
  df: 查看整个磁盘占有率
  [sun@hdp01 ~]$ hadoop fs -df -h /
  Filesystem            Size   Used  Available  Use%
  hdfs://hdp01:9000  137.2 G  200 K    118.2 G    0%
  
  
  ---------
  
  du: 每一个文件的大小
  [sun@hdp01 ~]$ hadoop fs -du -h / 
  270    /a.sh
  0      /a.txt
  1.5 K  /hdfs-site.xml
  309    /sun
  
  
  -count 个数
  ```





```
shell---命令行的操作方式
必须在hadoop的安装的节点上
hadoop  fs
        [-appendToFile <localsrc> ... <dst>]
        [-cat [-ignoreCrc] <src> ...]
        [-checksum <src> ...]
        [-chgrp [-R] GROUP PATH...]
        [-chmod [-R] <MODE[,MODE]... | OCTALMODE> PATH...]
        [-chown [-R] [OWNER][:[GROUP]] PATH...]
        [-copyFromLocal [-f] [-p] [-l] <localsrc> ... <dst>]
        [-copyToLocal [-p] [-ignoreCrc] [-crc] <src> ... <localdst>]
        [-count [-q] [-h] <path> ...]
        [-cp [-f] [-p | -p[topax]] <src> ... <dst>]
        [-createSnapshot <snapshotDir> [<snapshotName>]]
        [-deleteSnapshot <snapshotDir> <snapshotName>]
        [-df [-h] [<path> ...]]
        [-du [-s] [-h] <path> ...]
        [-expunge]
        [-find <path> ... <expression> ...]
        [-get [-p] [-ignoreCrc] [-crc] <src> ... <localdst>]
        [-getfacl [-R] <path>]
        [-getfattr [-R] {-n name | -d} [-e en] <path>]
        [-getmerge [-nl] <src> <localdst>]
        [-help [cmd ...]]
        [-ls [-d] [-h] [-R] [<path> ...]]
        [-mkdir [-p] <path> ...]
        [-moveFromLocal <localsrc> ... <dst>]
        [-moveToLocal <src> <localdst>]
        [-mv <src> ... <dst>]
        [-put [-f] [-p] [-l] <localsrc> ... <dst>]
        [-renameSnapshot <snapshotDir> <oldName> <newName>]
        [-rm [-f] [-r|-R] [-skipTrash] <src> ...]
        [-rmdir [--ignore-fail-on-non-empty] <dir> ...]
        [-setfacl [-R] [{-b|-k} {-m|-x <acl_spec>} <path>]|[--set <acl_spec> <path>]]
        [-setfattr {-n name [-v value] | -x name} <path>]
        [-setrep [-R] [-w] <rep> <path> ...]
        [-stat [format] <path> ...]
        [-tail [-f] <file>]
        [-test -[defsz] <path>]
        [-text [-ignoreCrc] <src> ...]
        [-touchz <path> ...]
        [-truncate [-w] <length> <path> ...]
        [-usage [cmd ...]]


```


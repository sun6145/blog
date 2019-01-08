---
title: redis02
tags:
  - null
  - null
date: 2018-09-21 09:01:19
categories:
top:
---



# 持久化

## RDB : Redis database

> 默认持久化方式,将内存数据隔固定时长存储到窗磁盘文件
>
> Redis默认的方式，redis通过快照来将数据持久化到磁盘中。

- 持久化文件存储的目录
     在redis.conf中可以指定持久化文件存储的目录默认dum.rdb

<!-- more -->

- 执行原理:

   1. fork子进程,父进程和子进程

   2. 父进程处理client连接,子进程将数据库内容存储到磁盘文件

      > 父进程创建snapshot(快照,共享信息),子进程处理的是snapshot的数据,写入到临时文件

  	3. 当子进程将临时文件写入完成,关闭子进程

  持久化策略:设置持久化快照的条件

  ```
  save 900 1     key修改了一次 900秒备份一次
  save 300 10		key修改了10次,300秒备份一次
  save 60  10000  key修改了10000 60秒备份一次
  ```

  缺点:

  ​	非法关闭,会丢失最后一次的持久化数据,如果数据不能允许丢失,那么要使用aof的方式

## AOF: append only file

> 是将每次**写操作**存储到aof持久化文件中,默认是关闭的,仍然会有可能会丢失数据(os也会有缓存),强制每次执行

> Aof方式的持久化，是操作一次redis数据库，则将操作的记录存储到aof持久化文件中。

### 第一步：开启aof方式的持久化方案

将redis.conf中的appendonly改为yes，即开启aof方式的持久化方案。

 启动: 672行 ;appendonly yes

Aof文件存储的目录和rdb方式的一样。

Aof文件存储的名称"`appendfilename "apendonly.aof"`



##  **结论**

在使用aof和rdb方式时，如果**redis重启**，则数据**从aof文件加载**。



#  Redis的主从复制



## 1.1 **什么是主从复制**

​	持久化保证了即使redis服务重启也不会丢失数据，因为redis服务重启后会将硬盘上持久化的数据恢复到内存中，但是当redis服务器的硬盘损坏了可能会导致数据丢失，如果**通过redis的主从复制机制就可以避免这种单点故障**，如下图：

![](https://raw.githubusercontent.com/sun6145/githubPicture/master/img/%E5%8D%95%E7%82%B9%E6%95%85%E9%9A%9C.png)

说明：

1. 主redis中的数据有两个副本（replication）即从redis1和从redis2，即使一台redis服务器宕机其它两台redis服务也可以继续提供服务。

2. 主redis中的数据和从redis上的数据保持实时同步，当主redis写入数据时通过主从复制机制会复制到两个从redis服务上。

3. 只有一个主redis，可以有多个从redis。

4. 主从复制不会阻塞master，在同步数据时，master 可以继续处理client 请求

5. 一个redis可以即是主又是从，如下图：

![](https://raw.githubusercontent.com/sun6145/githubPicture/master/img/redis%E5%8F%AF%E4%BB%A5%E5%8D%B3%E6%98%AF%E4%B8%BB%E5%8F%88%E6%98%AF%E4%BB%8E.png)

# 主从复制设置

## 主机配置

>  无需配置

## 从机配置

1. 复制出一个主机

   > cp  -r 主机  从机

2. 修该从机的redis.conf

   > 查找slaverof
   >
   > 添加配置信息:语法:`slaveof masterip masterport`
   >
   > slaveof 192.168.2.101 6379

3. 修改从机的port为6380

   > 在redis.conf文件中修改
   >
   > 搜索修改即可

4. 清除主机中的持久化文件  \*.rdb;\* aof

5. 根据从机的配置文件启动从机

   > ./redis-server redis.conf

6. 启动6380的客户端

   >  ./redis-cli -h 192.168.2.101 -p 6380  

- 注意

  > 主机一旦发生增删改操作，那么从机会将数据同步到从机中
  >
  > 从机不能执行写操作

  ```127.0.0.1:6380&gt; set s2 222
  127.0.0.1:6380> set s2 222
  (error) READONLY You can't write against a read only slave.
  ```

# Redis集群

##  redis-cluster架构图 

![](https://raw.githubusercontent.com/sun6145/githubPicture/master/img/redis%E9%9B%86%E7%BE%A4%E5%9B%BE.png)

```
架构细节

(1) 所有的redis节点彼此互联(PING-PONG机制),内部使用二进制协议优化传输速度和带宽.

(2)节点的fail是通过集群中 超过半数的节点检测 失效时才生效.

(3)客户端与redis节点直连,不需要中间proxy层.客户端不需要连接集群所有节点,连接集群中任何一个可用节点即可

(4)redis-cluster把所有的物理节点映射到[0-16383]slot上,cluster 负责维护node<->slot<->value

```

> Redis 集群中内置了 16384 个哈希槽，当需要在 Redis 集群中放置一个 key-value 时，redis 先对 key 使用 crc16 算法算出一个结果，然后把结果对 16384 求余数，这样每个 key 都会对应一个编号在 0-16383 之间的哈希槽，redis 会根据节点数量大致均等的将哈希槽映射到不同的节点



## redis-cluste 投票 :容错

![](https://raw.githubusercontent.com/sun6145/githubPicture/master/img/redis-cluster%E6%8A%95%E7%A5%A8.png)

```
(1)集群中所有master参与投票,如果半数以上master节点与其中一个master节点通信超过(cluster-node-timeout),认为该master节点挂掉.

(2):什么时候整个集群不可用(cluster_state:fail)? 
	1. 如果集群任意master挂掉,且当前master没有slave，则集群进入fail状态。也可以理解成集群的[0-16383]slot映射不完全时进入fail状态。

	2. 如果集群超过半数以上master挂掉，无论是否有slave，集群进入fail状态。

```

##  安装ruby

集群管理工具:（redis-trib.rb）是使用ruby脚本语言编写的。

### 第一步：安装ruby

```
[root@hdp01 bin2]# yum install ruby

[root@hdp01 bin2]# yum install rubygems
```



### 第二步：将以下文件上传到linux系统

 redis-3.0.0.gem

### 第三步,安装ruby和redis接口

`[root@hdp01 ~]# gem install redis-3.0.0.gem`



### 第四步：将redis-3.0.0包下src目录中的以下文件拷贝到redis19/redis-cluster/

```
[root@hdp01 src]# cd /usr/local/redis/
[root@hdp01 redis19]# mkdir redis-cluster
[root@hdp01 redis19]# cd /root/redis-3.0.0/src/
[root@hdp01 src]# cp redis-trib.rb  /usr/local/redis1/redis-cluster
```

## 搭建集群

```
搭建集群最少也得需要3台主机，如果每台主机再配置一台从机的话，则最少需要6台机器。

端口设计如下：6380-6385

第一步：复制出一个7001机器
[root@hdp01 redis]# cp bin ./redis-cluster/7001 –r

第二步：如果存在持久化文件，则删除
[root@hdp01 7001]# rm -rf appendonly.aof dump.rdb

第三步：设置集群参数
 cluster-enabled yes

第四步：修改端口
 port 6380
 
第五步：复制出7002-7006机器
[root@hdp01 redis-cluster]# cp 7001/ 7002 -r
[root@hdp01 redis-cluster]# cp 7001/ 7003 -r
[root@hdp01 redis-cluster]# cp 7001/ 7004 -r
[root@hdp01 redis-cluster]# cp 7001/ 7005 -r
[root@hdp01 redis-cluster]# cp 7001/ 7006 –r

第六步：修改7002-7006机器的端口

第七步：启动7001-7006这六台机器

第八步：修改start-all.sh文件的权限
[root@hdp01 redis-cluster]# chmod u+x start-all.sh

第九步：创建集群
[root@hdp01 redis-cluster]#  ./redis-trib.rb  create --replicas 1 192.168.2.102:6380 192.168.2.102:6381 192.168.2.102:6382 192.168.2.102:6383 192.168.2.102:6384 192.168.2.102:6385
>>> Creating cluster
Connecting to node 192.168.242.137:7001: OK
Connecting to node 192.168.242.137:7002: OK
Connecting to node 192.168.242.137:7003: OK
Connecting to node 192.168.242.137:7004: OK
Connecting to node 192.168.242.137:7005: OK
Connecting to node 192.168.242.137:7006: OK
>>> Performing hash slots allocation on 6 nodes...
Using 3 masters:
192.168.242.137:7001
192.168.242.137:7002
192.168.242.137:7003
Adding replica 192.168.242.137:7004 to 192.168.242.137:7001
Adding replica 192.168.242.137:7005 to 192.168.242.137:7002
Adding replica 192.168.242.137:7006 to 192.168.242.137:7003
M: 8240cd0fe6d6f842faa42b0174fe7c5ddcf7ae24 192.168.242.137:7001
   slots:0-5460 (5461 slots) master
M: 4f52a974f64343fd9f1ee0388490b3c0647a4db7 192.168.242.137:7002
   slots:5461-10922 (5462 slots) master
M: cb7c5def8f61df2016b38972396a8d1f349208c2 192.168.242.137:7003
   slots:10923-16383 (5461 slots) master
S: 66adf006fed43b3b5e499ce2ff1949a756504a16 192.168.242.137:7004
   replicates 8240cd0fe6d6f842faa42b0174fe7c5ddcf7ae24
S: cbb0c9bc4b27dd85511a7ef2d01bec90e692793b 192.168.242.137:7005
   replicates 4f52a974f64343fd9f1ee0388490b3c0647a4db7
S: a908736eadd1cd06e86fdff8b2749a6f46b38c00 192.168.242.137:7006
   replicates cb7c5def8f61df2016b38972396a8d1f349208c2
Can I set the above configuration? (type 'yes' to accept): yes
>>> Nodes configuration updated
>>> Assign a different config epoch to each node
>>> Sending CLUSTER MEET messages to join the cluster
Waiting for the cluster to join..
>>> Performing Cluster Check (using node 192.168.242.137:7001)
M: 8240cd0fe6d6f842faa42b0174fe7c5ddcf7ae24 192.168.242.137:7001
   slots:0-5460 (5461 slots) master
M: 4f52a974f64343fd9f1ee0388490b3c0647a4db7 192.168.242.137:7002
   slots:5461-10922 (5462 slots) master
M: cb7c5def8f61df2016b38972396a8d1f349208c2 192.168.242.137:7003
   slots:10923-16383 (5461 slots) master
M: 66adf006fed43b3b5e499ce2ff1949a756504a16 192.168.242.137:7004
   slots: (0 slots) master
   replicates 8240cd0fe6d6f842faa42b0174fe7c5ddcf7ae24
M: cbb0c9bc4b27dd85511a7ef2d01bec90e692793b 192.168.242.137:7005
   slots: (0 slots) master
   replicates 4f52a974f64343fd9f1ee0388490b3c0647a4db7
M: a908736eadd1cd06e86fdff8b2749a6f46b38c00 192.168.242.137:7006
   slots: (0 slots) master
   replicates cb7c5def8f61df2016b38972396a8d1f349208c2
[OK] All nodes agree about slots configuration.
>>> Check for open slots...
>>> Check slots coverage...
[OK] All 16384 slots covered.
[root@hdp01 redis-cluster]#
```



## 连接集群

>  `./redis-cli -h 192.168.2.102 -p 7001 –c`
>
> `-c：指定是集群连接`

##   查看集群信息

### 查看集群信息

> `192.168.242.137:7002> cluster info`

```
192.168.2.102:6381> cluster info
cluster_state:ok
cluster_slots_assigned:16384
cluster_slots_ok:16384
cluster_slots_pfail:0
cluster_slots_fail:0
cluster_known_nodes:6
cluster_size:3
cluster_current_epoch:6
cluster_my_epoch:2
cluster_stats_messages_sent:1034
cluster_stats_messages_received:1034
192.168.2.102:6381> 
```

### 查看节点信息

```
192.168.2.102:6381> cluster nodes
46ca0fd9417a2bc172aa2863df2cab0da6617d2d 192.168.2.102:6382 master - 0 1537696177521 3 connected 10923-16383
f53c4df09307ee23d5e06d9a7e936c581ac80b3d 192.168.2.102:6384 slave fed12b118c7d4c6ab95a3c2d3ffe49a71e9b2afa 0 1537696173493 5 connected
ef29970508e820fe2f7b7c2a57befc69e9ed2589 192.168.2.102:6385 slave 46ca0fd9417a2bc172aa2863df2cab0da6617d2d 0 1537696174499 6 connected
781aa9cb4deea53b8dce7405277d33176153fdc3 192.168.2.102:6383 slave d7cad0dd5dd1607eb4ca31e7be5344014d2a4cce 0 1537696176512 4 connected
fed12b118c7d4c6ab95a3c2d3ffe49a71e9b2afa 192.168.2.102:6381 myself,master - 0 0 2 connected 5461-10922
d7cad0dd5dd1607eb4ca31e7be5344014d2a4cce 192.168.2.102:6380 master - 0 1537696175505 1 connected 0-5460
192.168.2.102:6381> 
```

# jedis连接集群

```java
package test;

import java.util.HashSet;
import java.util.Set;

import org.junit.Test;

import redis.clients.jedis.HostAndPort;
import redis.clients.jedis.JedisCluster;

/**
 * jedis连接redis集群
 * @author sun
 *
 */
public class test3 {
	@Test
	public void test() {
		Set<HostAndPort> nodes = new HashSet<>();
		nodes.add(new HostAndPort("192.168.2.102", 6380));
		nodes.add(new HostAndPort("192.168.2.102", 6381));
		nodes.add(new HostAndPort("192.168.2.102", 6382));
		nodes.add(new HostAndPort("192.168.2.102", 6383));
		nodes.add(new HostAndPort("192.168.2.102", 6384));
		nodes.add(new HostAndPort("192.168.2.102", 6385));

		JedisCluster cluster = new JedisCluster(nodes);
		cluster.set("s4", "sunfuliang");

		String result = cluster.get("s4");
		System.out.println(result);
		cluster.close();
	}
}

```

# Spring jedis连接redis集群

```java
package test;

import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

import redis.clients.jedis.JedisCluster;

/**
 * jedis通过spring连接redis集群
 * 
 * @author sun
 *
 */
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations = "classpath:applicationContext.xml")
public class test4 {
	// redis集群
	@Autowired
	JedisCluster jedisCluster;
	@Test
	public void testJedisCluster() {

		jedisCluster.set("name", "zhangsan");
		String value = jedisCluster.get("name");
		System.out.println(value);
	}
}

```

# applicationContext.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="
                           http://www.springframework.org/schema/beans 
                           http://www.springframework.org/schema/beans/spring-beans.xsd
                           http://www.springframework.org/schema/context 
                           http://www.springframework.org/schema/context/spring-context.xsd
                           http://www.springframework.org/schema/aop
                           http://www.springframework.org/schema/aop/spring-aop.xsd">

    <!-- 连接池配置 -->
    <bean id="jedisPoolConfig" class="redis.clients.jedis.JedisPoolConfig">
        <!-- 最大连接数 -->
        <property name="maxTotal" value="30" />
        <!-- 最大空闲连接数 -->
        <property name="maxIdle" value="10" />
        <!-- 每次释放连接的最大数目 -->
        <property name="numTestsPerEvictionRun" value="1024" />
        <!-- 释放连接的扫描间隔（毫秒） -->
        <property name="timeBetweenEvictionRunsMillis" value="30000" />
        <!-- 连接最小空闲时间 -->
        <property name="minEvictableIdleTimeMillis" value="1800000" />
        <!-- 连接空闲多久后释放, 当空闲时间>该值 且 空闲连接>最大空闲连接数 时直接释放 -->
        <property name="softMinEvictableIdleTimeMillis" value="10000" />
        <!-- 获取连接时的最大等待毫秒数,小于零:阻塞不确定的时间,默认-1 -->
        <property name="maxWaitMillis" value="1500" />
        <!-- 在获取连接的时候检查有效性, 默认false -->
        <property name="testOnBorrow" value="false" />
        <!-- 在空闲时检查有效性, 默认false -->
        <property name="testWhileIdle" value="true" />
        <!-- 连接耗尽时是否阻塞, false报异常,ture阻塞直到超时, 默认true -->
        <property name="blockWhenExhausted" value="false" />
    </bean>

    <!-- redis集群 -->
    <bean id="jedisCluster" class="redis.clients.jedis.JedisCluster">
        <constructor-arg index="0">
            <set>
                <bean class="redis.clients.jedis.HostAndPort">
                    <constructor-arg index="0" value="192.168.2.102"></constructor-arg>
                    <constructor-arg index="1" value="6380"></constructor-arg>
                </bean>
                <bean class="redis.clients.jedis.HostAndPort">
                    <constructor-arg index="0" value="192.168.2.102"></constructor-arg>
                    <constructor-arg index="1" value="6381"></constructor-arg>
                </bean>
                <bean class="redis.clients.jedis.HostAndPort">
                    <constructor-arg index="0" value="192.168.2.102"></constructor-arg>
                    <constructor-arg index="1" value="6382"></constructor-arg>
                </bean>
                <bean class="redis.clients.jedis.HostAndPort">
                    <constructor-arg index="0" value="192.168.2.102"></constructor-arg>
                    <constructor-arg index="1" value="6383"></constructor-arg>
                </bean>
                <bean class="redis.clients.jedis.HostAndPort">
                    <constructor-arg index="0" value="192.168.2.102"></constructor-arg>
                    <constructor-arg index="1" value="6384"></constructor-arg>
                </bean>
                <bean class="redis.clients.jedis.HostAndPort">
                    <constructor-arg index="0" value="192.168.2.102"></constructor-arg>
                    <constructor-arg index="1" value="6385"></constructor-arg>
                </bean>
            </set>
        </constructor-arg>
        <constructor-arg index="1" ref="jedisPoolConfig"></constructor-arg>
    </bean>
    <!-- redis单机 通过连接池
 <bean id="pool" class="redis.clients.jedis.JedisPool"
  destroy-method="close">
  <constructor-arg name="poolConfig" ref="jedisPoolConfig" />
  <constructor-arg name="host" value="192.168.2.101" />
  <constructor-arg name="port" value="6379" />
 </bean> -->
</beans>

```


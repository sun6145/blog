---
title: redis01
tags:
  - null
  - null
date: 2018-09-20 09:24:16
categories:
top:
---



# nosql

> not only sql:解决海量数据和大型集群产生的高并发,高可用,高性能问题数据库解决方案,
>
> 非关系型数据库,不能替代关系型数据库,作为关系型数据库的一个良好补充(补充关系型数据库性能的瓶颈)



<!-- more -->

# 数据分类

- 结构化数据

  > 数据的结构一样,使用同一的规范(格式)进行处理和维护(存储和管理)(RDBMS)

- 半结构化数据

  > 介于二者之间的数据如xml

- 非结构化数据

  > 无法通过统一格式进行存储管理,如文本,音频,视频等(nosql)

# nosql的分类

1. 键值存储数据库

   产品: **Redis**,Voldemont,BERKEKEY DB'

   典型应用:内容缓存,主要用于处理大量数据的高访问负债

   数据模型: 一系列键值对

   ​		json:{"key":"value", "key2":"value2"}

   优势:快速查询

   劣势: 存储的数据缺少结构化

> 和memcache:的区别
>
> memcache: 数据类型少,做缓存
>
> redis:缓存数据,可以持久化,支持事务;数据类型多



2. 列存储数据库

   相关产品：Cassandra,**HBase**,Riak

   典型应用：分布式的文件系统

   数据模型：以列簇式存储，将同一列数据存在一起

   优势：查找速度快，可扩展性强，更容易进行分布式扩展

   劣势：功能相对局限

3. 文档型数据库

   相关产品：CouchDB、**MongoDB**

   典型应用：Web应用（与Key-Value类似，Value是结构化的）

   数据模型：一系列键值对

   优势：数据结构要求不严格

   愣势：查询性能不高，而且缺乏统一的查询语法

4. 图形（Graph)数据库

   相关数据库：**Neo4j**、InfoGrid、Infinite Graph

   典型应用：社交网络

   数据模型：图结构

   优势：利用图结构相关算法。

   劣势：需要对整个图做计算才能得出结果，不容易做分布式的集群方案。

# Redis 

> 是使用c语言开发的一个高性能键值数据库。Redis 可以通过一些键值类型来存储数据。也可以实现持久化支持事务操作

常用键值类型：

String 字符类型

map 散列类型

list 列表类型

set 集合类型

sortedset 有序集合类型(zset)

## redis的应用场景

缓存（数据查询、短连接、新闻内容、商品内容等等）。（最多使用）

分布式集群架构中的 session分离。

聊天室的在线好友列表。

任务队列。（秒杀、抢购、12306等等）

应用排行榜。

网站访问统计。

数据过期处理（可以精确到毫秒）



## redis的安装

- [下载](http://www.redis.cn/)

- 安装:

  1. 准备gcc环境 yum -y install gcc-c++

  2. make命令: yum -y install make

  3. 上传

  4. 解压:`tar -zxvf redis-4.0.10.tar.gz -C /opt/`

  5. 进入:` cd /opt/redis-4.0.10/`

  6. 编译:`make`  / `make distclean`(清理之前的编译)

     > ....>>/dev/null (黑洞) ,删掉的意思

  7. make test():测试(安装tcl)

  8. 安装 make install(默认安装`/usr/local/bin`)

     手动修改安装路径:`make install PREFIX=/usr/local/redis`

  9. `cd /usr/local/redis/bin`存放可执行的文件

  10. 赋值配置文件到etc目录下:`cp /opt/redis-4.0.10/redis.conf /etc/`

  11. 环境变量

      > `export REDIS_HOME=/usr/local/redis
      > export PATH=$PATH:$JAVA_HOME/bin:$REDIS_HOME/bin;`

## redis启动(服务器端+客户端)

### 前端启动

>  不推荐,(占用一个窗口),

> 在目录下`cd /usr/local/redis/bin`

- 启动服务端:`redis-server `
  - port:6379
- 关闭:
  1. 强制关闭:ctrl +C
  2. 客户端发送:./redis-cli发送 `shutdown`(推荐)
- 启动客户端 redis-cli 
  - 发送ping  响应PONG

### 后端启动

> 修改配置文件`vim /etc/redis.conf `

1. 修改绑定的ip(69行)

   > `bind 192.168.2.101`

2. 修改为台守护进程运行(136行)

   >  `daemonize yes`

3. databases 16: 分了16数据库

   ​	下标默认(0-15)

4. 启动

   > `redis-server /etc/redis.conf `

5. 查看

   1. ` ps -aux|grep redis`
   2. ` netstat -nltp`

6. 关闭服务端

   1. kill -9 6379
   2. redis-cli shutdown(推荐)

7. 自带客户端连接 

   > redis-cli -h ip地址 -p 端口
   >
   > redis-cli -h 192.168.2.101 -p 6379

8. 图形客户端

   > 需要注意防火墙



# jedis客户端

# Redis 数据类型

## 字符串String

### 命令

  1. 赋值和取值

     > set key value
     >
     > setget key value: 取值并赋值

  2. 删除

     > del key

  3. 数值增减

     > incr key  :增量1
     >
     > incrby key  n: 增量n
     >
     > decr: key :减少1
     >
     > decrby key n: 减少n

  4. 其他命令

     1. append key value : 追加值
     2. stlen key  :获取值得长度

### 应用:自增主键

> 商品编号,订单号采用string的递增数值特性生成

```
定义商品: key items:id
> incr items:id
(integer)2
> incr items:id
(integer)3
```



## hash类型

> 散列类型:支持扩展key,提供字段属性,字段,字段值之间的映射
>
> 字段值只支持字符串
>
> 键  字段名 字段值

### 赋值

- hset:一次只能设置一个字段
- hmset :一次可以设置多个字段值
- hsetnx:有该key则不赋值,没有则赋值相当于hset

> hset不区分插入和更新,0代表更新,1代表插入

```
192.168.2.101:6379> hset user:001 name zs
(integer) 1
192.168.2.101:6379> hset user:001 name ls
(integer) 0
192.168.2.101:6379> hmset user age 20 username lisi 
OK
```

### 取值

- hget: 一次只能取一个字段值
- HMGET key field [field ...] : 一次可以获取多个字段值
- hgetall key:  

```
192.168.2.101:6379> hget user:001 name
"zs"

 hmget user:sun name age
1) "sun"
2) "21"

192.168.2.101:6379> HGETALL user:001
1) "name"
2) "zs"
3) "sex"
4) "0"
```

### 删除字段

- hdel :删除字段

```
192.168.2.101:6379> hedl user:001 sex
(error) ERR unknown command 'hedl'
192.168.2.101:6379> hdel user:001 sex
(integer) 1
192.168.2.101:6379> HGETALL user:001
1) "name"
2) "zs"
```

### 增加数字

- hincrby

```
192.168.2.101:6379> hset user:001 age 15
(integer) 1

192.168.2.101:6379> hgetall user:001
1) "name"
2) "zs"
3) "age"
4) "15"

192.168.2.101:6379> hincrby user:001 age 3
(integer) 18
192.168.2.101:6379> hgetall user:001
1) "name"
2) "zs"
3) "age"
4) "18"
```

### 其他命令

#### 判断字段是否存在

>  haexists key 字段名 :1存在 ,0不存在

```
192.168.2.101:6379> HEXISTS user:001 sex
(integer) 0
192.168.2.101:6379> HEXISTS user:001 age
(integer) 1
```

#### 获取字段名或字段值

> 语法:
>
> ​	hkeys key
>
> ​	hvals key

```
192.168.2.101:6379> hkeys user:sun
1) "name"
2) "age"

192.168.2.101:6379> hvals user:sun
1) "sun"
2) "21"
```

#### 获取字段的数量

- hlen key

  ```
  192.168.2.101:6379> HLEN user:001
  (integer) 2
  ```

#### 应用

-  存储商品信息

  - 商品字段

    > 【商品id、商品名称、商品描述、商品库存、商品好评】

- 定义商品信息的key

  > 商品1001的信息在 Redis中的key为：[items:1001]

- 存储商品信息

  ```
  192.168.101.3:7003> HMSET items:1001 id 3 name apple price 999.9
  OK
  ```

- 获取商品信息

  ```
  192.168.101.3:7003> HGET items:1001 id
  "3"
  192.168.101.3:7003> HGETALL items:1001
  1) "id"
  2) "3"
  3) "name"
  4) "apple"
  5) "price"
  6) "999.9"
```

## list列表类型

### arraylist 和linkedlsit的区别

1. arraylist 是使用数组来存储数,特点:增删慢,查询快(连续空间)
2. linkedlist: 是使用双向链表存储数据,特定:增删快,查询慢,但是查询链表二端的数据也很快
3. redis的list采用链表来存储的数据,操作从二端操作

### 向列表二端添加元素

添加元素

- lpush key  value  : 向列表的左边增加元素

- rpush key  value: 向列表的右边增加元素

  ```
  192.168.2.101:6379> lpush list1 a b c
  (integer) 3
  192.168.2.101:6379> rpush list1 d e f
  (integer) 6
  192.168.2.101:6379> LRANGE list1 0 -1
  1) "c"
  2) "b"
  3) "a"
  4) "d"
  5) "e"
  6) "f"
  ```

### 从列表二端弹出元素

- `lpop key`

  > LPOP命令从列表左边弹出一个元素，会分两步完成：
  >
  > 第一步是将列表左边的元素从列表中移除
  >
  > 第二步是返回被移除的元素值。

- `rpop key`

```
192.168.2.101:6379> LPOP list1
"c"
192.168.2.101:6379> rpop list1
"f"
```

### 查看列表

>  **lrange命令**,获取列表中的某一片段，将返回start、stop之间的所有元素（包含两端的元素），索引从0开始。索引可以是负数，如：“-1”代表最后边的一个元素。

```
192.168.2.101:6379> lrange list1 0 2
1) "b"
2) "a"
3) "d"
192.168.2.101:6379> 

192.168.2.101:6379> lrange list1 0 -1
1) "b"
2) "a"
3) "d"
4) "e"
```

### 获取列表中元素的个数

语法:`llen key`

```
127.0.0.1:6379> llen list:1
(integer) 2
```

### 其他命令

#### 删除列表中指定的值

> LREM命令会**删除列表中前count个值为value的元素**，返回实际删除的元素个数。根据count值的不同，该命令的执行方式会有所不同： 
>
> 当count>0时， LREM会从列表左边开始删除。 
>
> 当count<0时， LREM会从列表后边开始删除。 
>
> 当count=0时， LREM删除所有值为value的元素。 

```
192.168.2.101:6379> LRANGE list1 0 -1
 1) "e"
 2) "d"
 3) "b"
 4) "b"
 5) "a"
 6) "e"
 7) "d"
 8) "b"
 9) "b"
10) "a"
11) "b"
12) "a"
13) "d"
14) "e"

192.168.2.101:6379> LREM list1 3 a
(integer) 3

192.168.2.101:6379> LRANGE list1 0 -1
 1) "e"
 2) "d"
 3) "b"
 4) "b"
 5) "e"
 6) "d"
 7) "b"
 8) "b"
 9) "b"
10) "d"
11) "e"
192.168.2.101:6379> LREM list1 0 b
(integer) 5
------------------------------------------------

192.168.2.101:6379> LRANGE list1 0 -1
1) "e"
2) "d"
3) "e"
4) "d"
5) "d"
6) "e"
------------------------------------------------
192.168.2.101:6379> LREM list1 -2 d
(integer) 2
192.168.2.101:6379> LRANGE list1 0 -1
1) "e"
2) "d"
3) "e"
4) "e"
192.168.2.101:6379> 
```

#### 获取/设置指定索引的元素值

语法:`lindex key  index `

```
192.168.2.101:6379> LRANGE list1 0 -1
1) "e"
2) "d"
3) "e"
4) "e"
192.168.2.101:6379> LINDEX list1 0
"e"
192.168.2.101:6379> LINDEX list1 -3
"d"
```



#### 保留片段

语法:`ltrim key start stop`

```
127.0.0.1:6379> lrange l:list 0 -1
1) "6"
2) "5"
3) "0"
4) "2"
127.0.0.1:6379> ltrim l:list 0 2
OK
127.0.0.1:6379> lrange l:list 0 -1
1) "6"
2) "5"
3) "0"
```



#### 插入某个元素的前后位置

语法:`linsert  key before|after pivot value`

> 该命令首先会在列表中从左到右查找值为pivot的元素，然后根据第二个参数是BEFORE还是AFTER来决定将value插入到该元素的前面还是后面。 

```
127.0.0.1:6379> lrange list 0 -1
1) "3"
2) "2"
3) "1"

127.0.0.1:6379> linsert list after 3 4
(integer) 4

127.0.0.1:6379> lrange list 0 -1
1) "3"
2) "4"
3) "2"
4) "1"
```

#### 将元素从一个列表转移到另一个列表

语法:`rpoplpush source destionation`

```
127.0.0.1:6379> rpoplpush list newlist 
"1"
127.0.0.1:6379> lrange newlist 0 -1
1) "1"
127.0.0.1:6379> lrange list 0 -1
1) "3"
2) "4"
3) "2"
```



### 应用

```
商品评论列表
思路：
在Redis中创建商品评论列表
用户发布商品评论，将评论信息转成json存储到list中。
用户在页面查询评论列表，从redis中取出json数据展示到页面。

定义商品评论列表key：
商品编号为1001的商品评论key【items: comment:1001】
192.168.101.3:7001> LPUSH items:comment:1001 '{"id":1,"name":"商品不错，很好！！","date":1430295077289}'
```







## set集合类型

> 集合类型
>
> 集合类型：无序、不可重复
>
> 列表类型：有序、可重复

### sadd 添加和删除

语法:`SADD key member [member ...]`

语法:`SREM key member [member ...]`

### 获得集合中的所有元素

语法:`smembers key`



- sismember key  member 判断是否在其中

  ```
  192.168.2.101:6379> sadd set1 a b c d
  (integer) 4
  
  192.168.2.101:6379> SREM set1 a
  (integer) 1
  192.168.2.101:6379> SMEMBERS set1
  1) "c"
  2) "b"
  3) "d"
  
  192.168.2.101:6379> SISMEMBER set1 c
  (integer) 1
  ```

### 判断元素是否在集合中

语法:`sismemner key member`

### 运算命令



#### 并集

- sunion key1 key2

#### 交集

- sinter key1 key2

#### 差集

- sdiff key1 key2

### 其他命令

#### 获得集合中的元素个数

- 语法:`scard key`

#### 从集合中弹出一个元素

> 注意：由于集合是无序的，所有SPOP命令会从集合中随机选择一个元素弹出 

- 语法:`spop key`

- spop:弹出: 无序,随便弹(相当于删除一个元素)

## sortedset

> Sortedset 又叫zset
>
> 是有序集合,可排序,但是唯一
>
> Sortedset和set的不同之处，是会给set中的元素添加一个分数，然后通过这个分数进行排序。

### 添加元素

> 向有序集合中加入一个元素和该元素的分数，如果该元素已经存在则会用新的分数替换原有的分数。返回值是新加入到集合中的元素个数，不包含之前已经存在的元素。 

> 添加元素和分数,如果元素存在则修改分数并返回0 ,不存在返回1

语法:`ZADD key score member [score member ...]`



```
192.168.2.101:6379> ZADD zset1 80 zs 90 ls 100 ww
(integer) 3
192.168.2.101:6379> ZADD zset1 85 zs
(integer) 0
192.168.2.101:6379> ZADD zset1 75 zl
(integer) 1
```

### 删除元素

> 移除有序集key中的一个或多个成员，不存在的成员将被忽略。
>
> 当key存在但不是有序集类型时，返回一个错误。

- `ZREM key member [member ...]`

```
192.168.2.101:6379> zrem zset1 zs
(integer) 1
```

### 查看元素的分数

- `zscore key member`

```
192.168.2.101:6379> ZSCORE zset1 zs
"85"
```

### 获取元素的排名

- 从小到大: 语法`zrank key member`
- 从大到小:语法`zrevrank key member`

### 获取范围内的元素列表

语法:`zrange key  start stop [withscores] `

> 按照元素分数**从大到小**的顺序返回索引从start到stop之间的所有元素（包含两端的元素）

```
192.168.2.101:6379> ZRANGE zset1 0 -1
1) "zl"
2) "ls"
3) "ww"

192.168.2.101:6379> ZRANGE zset1 0 -1 withscores
1) "zl"
2) "75"
3) "ls"
4) "90"
5) "ww"
6) "100"
```


- 倒序输出

  - `zrevrange key start stop [withscores] `

  ```
  192.168.2.101:6379> ZREVRANGE zset1 0 -1
  1) "ww"
  2) "ls"
  3) "zl"
  192.168.2.101:6379> ZREVRANGE zset1 0 -1 withscores
  1) "ww"
  2) "100"
  3) "ls"
  4) "90"
  5) "zl"
  6) "75"
  ```

### 其他命令

#### 获取指定分数范围内的元素

语法:`zrangebyscore key min max [withscires] limit offset count`

```
127.0.0.1:6379> ZRANGEBYSCORE scoreboard 90 97 WITHSCORES
1) "wangwu"
2) "94"
3) "lisi"
4) "97"
127.0.0.1:6379> ZRANGEBYSCORE scoreboard 70 100 limit 1 2
1) "wangwu"
2) "lisi"
```

#### 增加某个元素的分数

> 返回值是更改后的分数

语法: `zincrby key increment member`

```
127.0.0.1:6379> ZINCRBY scoreboard 4 lisi 
"101“
```

#### 获取集合中元素的数量

语法:`zcard key`

```
127.0.0.1:6379> ZCARD scoreboard
(integer) 3
```



#### 获取指定分数范围内的元素个数

语法:`zcount key min max`

```
127.0.0.1:6379> ZCOUNT scoreboard 80 90
(integer) 1
```

#### 按照排名范围删除元素

语法:`zremrangebyrank key start stop `

```
127.0.0.1:6379> ZREMRANGEBYRANK scoreboard 0 1
(integer) 2 
127.0.0.1:6379> ZRANGE scoreboard 0 -1
1) "lisi"
```

#### 按照分数范围删除元素

语法:`zremrangebyscore key min max`

```
127.0.0.1:6379> zadd scoreboard 84 zhangsan	
(integer) 1
127.0.0.1:6379> ZREMRANGEBYSCORE scoreboard 80 100
(integer) 1
```

### 应用

#### 商品销售排行榜

```
需求：根据商品销售量对商品进行排行显示
思路：定义商品销售排行榜（sorted set集合），Key为items:sellsort，分数为商品销售量。

写入商品销售量：
商品编号1001的销量是9，商品编号1002的销量是10
192.168.101.3:7007> ZADD items:sellsort 9 1001 10 1002

商品编号1001的销量加1
192.168.101.3:7001> ZINCRBY items:sellsort 1 1001

商品销量前10名：
192.168.101.3:7001> ZRANGE items:sellsort 0 9 withscores
```
###
##


# KEYS命令

## 常用命令

### keys

语法:`keys parttern`

```
返回满足给定pattern 的所有key
redis 127.0.0.1:6379> keys mylist*
1) "mylist"
2) "mylist5"
3) "mylist6"
4) "mylist7"
5) "mylist8"
```



### exists

> 判断key是否存在,0不存在,1存在

```
redis 127.0.0.1:6379> exists HongWan
(integer) 0
redis 127.0.0.1:6379> exists age
(integer) 1
redis 127.0.0.1:6379>


从结果来看，数据库中不存在HongWan 这个key，但是age 这个key 是存在的
```



### del

> 删除一个key

```
redis 127.0.0.1:6379> del age
(integer) 1
redis 127.0.0.1:6379> exists age
(integer) 0
```



### rename

> 重命名key

```
age 成功的被我们改名为age_new
redis 127.0.0.1:6379[1]> keys *
1) "age"
redis 127.0.0.1:6379[1]> rename age age_new
OK
redis 127.0.0.1:6379[1]> keys *
1) "age_new"
redis 127.0.0.1:6379[1]>
```



### type

> 返回值的类型

```
redis 127.0.0.1:6379> type addr
string
redis 127.0.0.1:6379> type myzset2
zset
redis 127.0.0.1:6379> type mylist
list
redis 127.0.0.1:6379>
```



## 设置key的生存时间

> 缓存的数据一般都是需要设置生存时间,到期后数据笑话

- expire key seconds 设置key秒之后销毁
- ttl key 查看key的生存时间:默认-1 ,销毁后为-2

```
EXPIRE key seconds			 设置key的生存时间（单位：秒）key在多少秒后会自动删除
TTL key 					查看key生于的生存时间
PERSIST key				清除生存时间 
PEXPIRE key milliseconds	生存时间设置单位为：毫秒
```

```
192.168.101.3:7002> set test 1		设置test的值为1
OK
192.168.101.3:7002> get test			获取test的值
"1"
192.168.101.3:7002> EXPIRE test 5	设置test的生存时间为5秒
(integer) 1
192.168.101.3:7002> TTL test			查看test的生于生成时间还有1秒删除
(integer) 1
192.168.101.3:7002> TTL test
(integer) -2
192.168.101.3:7002> get test			获取test的值，已经删除
(nil)
```




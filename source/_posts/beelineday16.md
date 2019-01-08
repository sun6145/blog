---
title: beeline day16
tags:
  - null
  - null
date: 2018-10-30 11:22:42
categories:
top:
---

# hive的beeline的连接方式:

> beeline：远程连接的服务  hive中内置了这个工具

<!-- more -->

## 步骤

1. 修改hadoop集群的配置 

   > hadoop集群中的每一个节点的配置文件都需要修改
   >
   > 将下面的配置文件  远程发送到其他hadoop节点


```
1）修改hdfs-site.xml
    <property>
    <name>dfs.webhdfs.enabled</name>
    <value>true</value>
    </property>

2)core-site.xml
    <property>
    <name>hadoop.proxyuser.hadoop.hosts</name>
    <value>*</value>
    </property>

    <property>
    <name>hadoop.proxyuser.hadoop.groups</name>
    <value>*</value>
    </property>
    
scp hdfs-site.xml core-site.xml 
	hadoop02:/home/hadoop/apps/hadoop-2.7.6/etc/hadoop/
scp hdfs-site.xml core-site.xml 
	hadoop03:/home/hadoop/apps/hadoop-2.7.6/etc/hadoop/
scp hdfs-site.xml core-site.xml 
	hadoop04:/home/hadoop/apps/hadoop-2.7.6/etc/hadoop/
	
重启hadoop集群
```

## 启动hive

1. 先启动hive的服务端  为后台服务，一定在**安装hive的节点启动的**
   `hiveserver2`   启动hive的服务端

2. 启动beeline进行连接:`beeline`

3. 连接：

   `!connect hive的url:	jdbc:hive2://hadoop02:10000`

   `mysql的url：jdbc:mysql://hadoop02:3306`

4. 下面的提示：

   ```
   Enter username for jdbc:hive2://hadoop02:10000:安装hive的用户的名字
   Enter password for jdbc:hive2://hadoop02:10000:安装hive的用户的密码
   
   下面日志证明连接成功：
   Connected to: Apache Hive (version 2.3.2)
   Driver: Hive JDBC (version 2.3.2)
   Transaction isolation: TRANSACTION_REPEATABLE_READ
   0: jdbc:hive2://hadoop02:10000>
   hive>
   ```


## hive后台服务
	nohup hiveserver2 1>/home/hadoop/logs/hiveserver.log 2>/home/hadoop/logs/hiveserver.err &
	
	解释：
	nohup   no hang up 
	输出日志级别为1   代表标准输出
	输出级别为2  代表的是错误输出

## 其他节点通过beeline连接

- 使用beeline连接的时候：
  1. 将hive的安装包远程发送到其他的节点上
  2. 启动beeline：到bin目录下  ./beeline启动

# transform

> hive的**脚本解析**方式
>
> 脚本：shell python写的脚本

## 解析某一个字符串/数据：
1. hive内置函数
2. 自定义函数
3. 可以将解析任务写在一个脚本中  ,hive中调用transform的这种方式运行脚本

```
需求：
统计周一到周日哪一天的观影人数最多
问题：将时间戳转换为周几
用transform的方式进行解析：
			
准备数据：
{"movie":"2355","rate":"5","timeStamp":"978824291","uid":"1"}	
{"movie":"1197","rate":"3","timeStamp":"978302268","uid":"1"}
{"movie":"1287","rate":"5","timeStamp":"978302039","uid":"1"}	
{"movie":"2804","rate":"5","timeStamp":"978300719","uid":"1"}
{"movie":"594","rate":"4","timeStamp":"978302268","uid":"1"}

建表关联数据：
create table movie_01(line string);
load data local inpath '/home/hadoop/datas/movie' into table movie_01;

json解析原始表：
create table movie_02 as 
select 
get_json_object(line,'$.movie') movieid,
get_json_object(line,'$.rate') rate,
get_json_object(line,'$.timeStamp') time01,
get_json_object(line,'$.uid') userid 
from movie_01;

```

```python
transform解析：
1）写脚本进行解析时间戳  python
## vi weekday_mapper.py
centos6   默认安装python

#!/usr/bin/python
import sys
import datetime
for line in sys.stdin:
line = line.strip()
movie,rate,unixtime,userid = line.split('\t')
weekday = datetime.datetime.fromtimestamp(float(unixtime)).isoweekday()
print '\t'.join([movie, rate, str(weekday),userid])

strip():相当于java中的trim  去前后空格的		
concat_ws("\t",)
```

```
2)在hive中调用脚本进行解析

1. 将脚本文件加载到hive的classpath下
	add file /home/hadoop/datas/my.py;
2. 检验：
    list files;
    list file;
```

```
3)查询解析

create table movie_03 as 
select 
transform(movieid,rate,time01,userid) using 'python my.py' as 
(movieid,rate,week_day,userid) 
from movie_02;

using 跟的是脚本执行语言：
shell脚本：sh ...sh
python脚本：python ...py

最终语句：
select week_day,count(1) as total from movie_03 
group by week_day order by total desc limit 1;
```





# hive的多字节分隔符

> hive中默认只支持单字节分割符  **不支持多字节分割符**

```
数据：
	1::zs::23
	2::ls::32
	3::ww::45

建表：
create table test_byte(id int,name string,age int) 
row format delimited fields terminated by '::';

数据加载：
load data local inpath '/home/hadoop/datas/test_01' into table test_byte;
结果：
	1::zs::23
	1  空 zs 空 23
查询
	1  空 zs（转换失败为null）
	1      ( 空)    NULL
	2               NULL
	3               NULL
默认是不支持多字节分割符的
想要支持  需要自定义输入  输出
```

## 正则表达式的方式进行解析

```
默认的输入和输出：
	默认的输入：一整行文件数据
		分割符：“：”
		默认输出：1  空 zs 空 23
表的默认描述信息中：
SerDe Library:org.apache.hadoop.hive.serde2.lazy.LazySimpleSerDe  只能解析单字节     
		InputFormat: org.apache.hadoop.mapred.TextInputFormat         
		OutputFormat: org.apache.hadoop.hive.ql.io.HiveIgnoreKeyTextOutputFormat
---------------------
想要解析多字节：正则表达式的方式进行解析
定义：
	1）解析类-----正则表达式的解析类   org.apache.hadoop.hive.serde2.RegexSerDe
	2）修改输入类：
		自定义输入:输入格式就是一个正则 表达式
        1::zs::23----> (.*)::(.*)::(.*)
	3)修改输出类：
		自定义输出:取正则表达式解析完成的
		%1$s %2$s %3$s

```

```sql
最终建表：
create table t_bi_reg(id int,name string,age int) 
row format serde 'org.apache.hadoop.hive.serde2.RegexSerDe'
with serdeproperties('input.regex'='(.*)::(.*)::(.*)',
'output.format.string'='%1$s %2$s %3$s') 
stored as textfile;	

加载数据：
load data local inpath '/home/hadoop/datas/test_01' into table t_bi_reg;
```

## 多字节分割符的处理方式

1. 将多字节分割符转换为单字节分割符
   > **有风险,需要足够了解数据**
   > 原始数据：1::zs:se::23
   > 将`::---->:`得到`1:zs:se:23`

  2. 自定义输入 输出方式  解析类-----正则表达式的解析类

     > **常用   没有风险**

3. 修改源码--全局作用

    > **基本不用**
    > 数据的分割符 各色各样的
    > 比较麻烦的

# hive的分析函数

> 分析函数：统计分析的时候使用的

## over子句

> 场景：
>
> ​	分组求topN
> ​	按地区求排名前几的
> ​	每一门课程平均分的排前三



```
需求：求每一个部门中年龄最小的前三个
		分组：部门
		排序：年龄  升序
sql实现：
		select 
		department,age 
		from stu_test01 group by department 
		order by age limit 3;  //全局的升序排序
		错误的写法
		实际需求：
			按照部门分组  在每一个部门中进行排序 组内的排序
```

```
over 子句--窗口函数
		把每一个部门中的数据单独拿出来进行排序分析
		可以理解为将每一个部门的数据单独放在一个分析的窗口中
		可以指定，每一个窗口的取数据的分组条件和排序条件的
			每一个窗口中取数据的分组条件 partition by/distribute by 
			每一个窗口内部的排序规则  order by(partition by)/sort by(distribute by)
		
over子句不能单独使用，必须和聚合函数sum  count一起使用
	或者和row_number rank   dense_rank一起使用
```

## row_number 

> ## 行号/排名的


```sql
select department,age,
	row_number() over(partition by department order by age asc)  
from stu_test01;

结果：
	CS      18      1
	CS      18      2
	CS      19      3
	CS      19      4
	CS      19      5
	CS      19      6
	CS      20      7
	CS      20      8
	CS      21      9
	CS      23      10
	IS      18      1
	IS      18      2
	IS      19      3
	IS      19      4
	IS      19      5
	IS      19      6
	IS      19      7
	IS      19      8
	IS      19      9
	IS      19      10
	IS      21      11
	
年龄最小的前三：
select department,age,b.index  
from (select department,age,row_number() over(partition by department order by age asc) index  from student_external) b
where b.index<=3;

```

 ## rank

>  ## 排名   跳过并列的  并列的排名是一样的

    select department,age,
    	rank() over(partition by department order by age asc)  
    from student_external;
    结果：
    CS      18      1
    CS      19      2
    CS      19      2
    CS      20      4
    CS      20      4
    CS      21      6
    CS      23      7
    IS      18      1
    IS      19      2
    IS      19      2
    IS      19      2
    IS      19      2
    IS      21      6
    MA      17      1
    MA      18      2
    MA      18      2
    MA      18      2
    MA      18      2
    MA      19      6
    MA      20      7
    MA      22      8
## dense_rank 

> 并列的排名一样的  排名不跳  顺序递增的

```
select department,age,dense_rank() over(partition by department order by age asc)  
from student_external;
	CS      18      1
	CS      18      1
	CS      19      2
	CS      19      2
	CS      19      2
	CS      19      2
	CS      20      3
	CS      20      3
	CS      21      4
	CS      23      5
	IS      18      1
	IS      18      1
	IS      19      2
	IS      19      2
	IS      19      2
	IS      19      2
	IS      19      2
	IS      19      2
	IS      19      2
	IS      19      2
	IS      21      3
```

# 补充：
1. hive中表中的每一个字段之间默认的分割符  \t

2. hive内置函数中  类型转换函数cast   针对可以转的类型
   `cast(需要转换的字段 as 转换的类型)`

3. union 、union all
   ​	将多个查询结果进行联合拼接
   ​	`union`去重
   ​	`union all` 不去重的

   ```
   select * from student_external where  age=18 
   union 
   select * from student_external where sex='女';
   
   95002   刘晨    女      19      IS
   95003   王敏    女      22      MA
   95005   刘刚    男      18      MA
   95007   易思玲  女      19      MA
   95008   李娜    女      18      CS
   95009   梦圆圆  女      18      MA
   95011   包小柏  男      18      MA
   95012   孙花    女      20      CS
   95014   王小丽  女      19      CS
   95015   王君    男      18      MA
   95017   王风娟  女      18      IS
   95018   王一    女      19      IS
   95019   邢小丽  女      19      IS
   ```

   ```
   select * from student_external where age=18 
   union all 
   select * from student_external where sex='女';
   
   95002   刘晨    女      19      IS
   95017   王风娟  女      18      IS
   95017   王风娟  女      18      IS
   95018   王一    女      19      IS
   95014   王小丽  女      19      CS
   95019   邢小丽  女      19      IS
   95003   王敏    女      22      MA
   95012   孙花    女      20      CS
   95005   刘刚    男      18      MA
   95007   易思玲  女      19      MA
   95008   李娜    女      18      CS
   95008   李娜    女      18      CS
   95011   包小柏  男      18      MA
   95009   梦圆圆  女      18      MA
   95009   梦圆圆  女      18      MA
   95015   王君    男      18      MA
   ```




4. sql  hql---查询
   1. 写查询语句：
      - 查询语句顺序
      - 分组   排序 过滤  需求字段
      - `select  *** from stu_test01 where group by order by limit `
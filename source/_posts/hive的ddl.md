---
title: hive的操作
tags:
  - null
  - null
date: 2018-10-29 16:25:00
categories: HIVE
top:
---

1. hive的DDL  
   1. 表的创建，修改
2. DML
   1. 数据的加载（load/insert）
3. 数据导出

<!-- more -->



# hive的ddl

> data-define-language

## 数据库

1. 建库：
   ​	`create database if not exists test;`
2. 切换数据库：
   ​	`use test;`
3. 查看正在使用的数据库：
   ​	`select current_database();`
  4. 查看数据库列表：
     ​	`show databases;`
     ​	`show databases like 'test*';`
 5. 查看数据库的详细描述信息：
    ​	`desc database test;----DBS`
6. 删除库：
   ​	`drop database if exists test; ` 只能删除空的数据库
   ​	`drop database test restrict; `.   restrict
   ​	`drop database test cascade;`   强制删除非空数据库
7. 修改数据库：**`不支持`**

## 表

1. 显示表列表：
   ​	`show tables;`
   ​	`show tables like 'test*';`
   ​	`show tables in test_home; `  查看指定数据库下的表

   ​	`show partitions table_name;查看指定分区表的分区信息`

   ​	`show partitions student_ptn partition(department="IS");`查看表分区

2. 查看表的详细信息：
   ​	`desc test01;`  可以查看表的字段描述信息
   ​	`desc extended test01;`  查看表的详细信息
   ​	`desc formatted test01;`  格式化显示表的详细信息

3. 删除表
   ​	`drop table test01;`

4. 清空表
   ​	`truncate table test01;`清空表中的数据  表结构保存

5. 查看表的建表语句

   ​	`show create table tablename;`最全的建表语句

### 表的创建

```java
CREATE [EXTERNAL] TABLE [IF NOT EXISTS] table_name
		 [(col_name data_type [COMMENT col_comment], ...)]
		 [COMMENT table_comment]
		 [PARTITIONED BY (col_name data_type [COMMENT col_comment], ...)]
		 [CLUSTERED BY (col_name, col_name, ...)
		 [SORTED BY (col_name [ASC|DESC], ...)] INTO num_buckets BUCKETS]
		 [ROW FORMAT row_format]
		 [STORED AS file_format]
		 [LOCATION hdfs_path]

解析：
	1）EXTERNAL   创建外部表需要指定的关键字  不指定默认创建的是内部表
	2）if not exists   避免报错的  建表的时候指定
		if exists     删除表的时候指定
	3）comment   添加描述信息
	4）PARTITIONED BY：
		指定分区字段的   
		注意：分区字段一定不能再表的原始建表字段中
		建表的时候3个字段：id   name   age  指定分区字段的一定不能是上面的三个字段
	5）[CLUSTERED BY (col_name, col_name, ...)  指定分桶字段
		注意：分桶字段一定在建表字段中的某一个或几个
		[SORTED BY (col_name [ASC|DESC], ...)]  每一个桶中的排序规则
		INTO num_buckets BUCKETS]  指定桶的个数的
	6）ROW FORMAT row_format  指定分割符的
		fields terminated by 
		lines terminated by 
		collections terminated by 
	7)STORED AS file_format  指定hive表中的原始数据的存储格式
		默认的textFile进行文件存储
		rcfile
	8)LOCATION hdfs_path     指定hive表中的数据的存储目录	
		默认目录：
			/user/hive/warehouse/...db
		修改配置文件进行修改：全局的  针对所有的表的数据
			/user/hive/hivehome/...db/stu
		这里的location也可以指定数据的存储目录，针对某一个表
			这里指定的时候覆盖配置文件中的  不指定默认配置文件中的路径
		注意：这里指定的路径一定是hdfs的路径  而不是本地
```

### 建表例子

1）创建一个内部表

```sql
create table if not exists student_managed(id int,name string,sex string,age int,department string) 
row format delimited fields terminated by ',' location '/user/mytable/stu';
```

2）创建一个外部表

```sql
create external table student_external(id int,name string,sex string,age int,department string) 
row format delimited fields terminated by ',';
```

3）创建一个分区表

> 分区字段：选择的时候 根据实际需求：经常根据哪一个字段进行过滤查询
> 外部的分区表  分区字段department
>
> 一个目录下创建不同的目录

```sql
create external table if not exists student_ptn(id int,name string,sex string,age int) 
partitioned by(department string) 
row format delimited fields terminated by ',';
```

4）创建一个分桶表

> 相当于mapreduce的分区的   key.hash%tasks
>
> 一个目录下创建不同的文件

```sql
create external table if not exists student_buk
	(id int,name string,sex string,age int,department string) 
clustered by (id) sorted by (age asc) into 3 buckets 
row format delimited fields terminated by ',';

桶的个数：3   id%3   
指定分桶字段：id
按照age  asc 升序排序
```

5）进行表复制

> 关键字：like
> **只会复制表结构 不会复制表数据**
>
> 内部表还是外部表取决于表的自身的属性	

```sql
create table if not exists student_copy like student_external;	//没有external，内部表
```

6）ctas进行建表

> 查询语句需要存储为一个临时表 
>
> `create table .....  as   select ....   from .....where .....`



### 表的修改操作

- 修改表名

  ​	`alter table student_copy rename to stu_copy;`

- 修改表的列信息

  - 增加列

    `alter table stu_copy add columns (salary double);`

  - 替换列：将所有的列替换为新给的列

    `alter table stu_copy replace columns(test01 int,test02 string);`

  - 修改列

    1. 修改列名

    2. 修改列的类型:类型修改的时候  小类型---大类型

       `alter table stu_copy change test01 aa01 int;`

       `alter table stu_copy change aa01 aa01 string;`

       ~~`alter table stu_copy change aa01 aa01 int;`~~不支持String转为int



### 修改表的分区信息

#### 增加/添加表的分区

> `add partition(分区信息),注意：分区信息是指定分区字段的值`

```sql
alter table student_ptn add partition(department="IS");
alter table student_ptn add partition(department="MA");
alter table student_ptn add partition(department="CS");

一次性添加多个分区
alter table student_ptn add partition(department="CS") partition(department="") partition(department="");
```

#### 修改分区的存储位置

> 分区默认存储位置：/user/hive/hivehome/bd1807.db/student_ptn/department=CS

```sql
1）添加分区的时候使用location关键字进行指定
alter table student_ptn add partition(department="test")
	location '/user/hive/hivehome/test_ptn';
				
2)针对已经添加的分区   可以手动修改存储目录  *******
alter table student_ptn partition(department="test") 
	set location '/user/hive/hivehome/test_ptn';
```

#### 删除分区

```
alter table student_ptn drop partition(department="test");
```



# hive的dml

## 数据加载

> 以下两种方式最后都是将数据文件放到hive表中的目录下

如果在建表的时候指定了目录  这个目录下有数据   当前表中有没有数据？

```
create table if not exists student_test02(id int,name string,sex string,age int,department string) 
row format delimited fields terminated by ',' 
location '/hive_in';
查询的时候  发现表中是有数据的
```



### load方式

> **本质**：将hdfs的原始文件的数据移动到hive的表的目录下
> ​	在移动的过程中如果发现重名的 重命名  新的数据文件命名为student_copy_1.txt

语法：

```
LOAD DATA [LOCAL] 
	INPATH 'filepath' 
	[OVERWRITE] INTO TABLE tablename 
	[PARTITION(partcol1=val1, partcol2=val2 ...)]
	
解析：	
	1）local关键字
		这个关键字指定数据从本地加载的  数据不是从本地加载  这个关键字不要
	2）inpath 后面的路径
		绝对路径
	3）OVERWRITE关键字  是否覆盖原表数据进行数据插入  加上这个关键字就会覆盖原始表中的数据
	

```

#### load实例

##### 本地数据加载到hive表中

	load data local inpath '/home/hadoop/datas/student.txt' into table student_managed;
	
	load data local inpath '/home/hadoop/datas/student.txt' overwrite into table student_managed;
##### 从hdfs进行数据加载

```sql
load data inpath '/hive_in/student.txt' into table student_managed;
```

### insert方式

#### 单条数据插入

> 每次只插入一条数据

```sql
insert into table student_managed values(123,"zss","男",34,"IS");
将插入的数据先放在一个临时表中  在从临时表中将数据load到要插入的表中
```

#### 单重数据插入

```sql
一次插入多条数据
insert into table ... select ....  from ...  where ...

insert into table stu_test select * from student_managed where age>20;
```

#### 多重数据插入

```sql
insert into table stu_test select * from student_managed where age>20;
insert into table stu_test01 select * from student_managed where age<20;
合并的写法
from student_managed 
insert into stu_test select * where age>20 
insert into stu_test01 select * where age<20;

语法：
from ...
insert into table ... select .... where ...
insert into table ..... select .... where...
....
```

### 单级分区表的数据插入

#### 静态分区

> 适用于分区数量比较少  分区名字都知道的时候
>
> 静态分区的数据插入---插入数据的时候指定分区的字段名

1. 添加分区`alter table student_ptn add partition(department='IS');`
2. 向指定表的分区中插入数据,加载到了表的某一个分区中

##### load的方式

> **不会对加载的数据进行判断筛选**
>
> 这种方式在进行加载的时候**一定要十分确定是这个分区的数据**  

```sql
load data local inpath '/home/hadoop/datas/student.txt' 
into table student_ptn partition(department='IS');

查询表：会把分区字段看做普通字段
select * from student_ptn; （定义4个字段，结果5个字段）
前4个字段就是我们加载文件的前4个列的值  最后一个字段分区字段department='IS'
```

##### insert的方式

> 从一个表中进行**查询数据放在分区表**中的  使用insert

```sql
单重数据插入
insert into table student_ptn partition(department="IS") 
select id,name,sex,age from student_managed where department="IS";

多重数据插入--数据插入的时候也会自动创建指定的分区
from student_managed 
insert into table student_ptn partition(department="IS") select id,name,sex,age where department="IS" 
insert into table student_ptn partition(department="MA") select id,name,sex,age where department="MA";

在查询的时候是全表扫描
	select*   全表扫描
将分区字段看做普通字段进行查询,
	select * ...where department="IS"  
	只会扫描分区为department="IS"  
	整体的查询性能比普通表高
查询的过滤条件不是分区字段的时候
select *    where age=20   ----全表扫描 
```

#### 动态分区

> 插入分区表数据的时候  不需要指定分区名的
>
> 动态分区的数据插入**只能使用insert的方式**
>
> 语法：`partition  指定分区字段`

```sql
insert into table student_ptn partition(department) select * from student_managed;

报错：
FAILED: SemanticException [Error 10096]: Dynamic partition strict mode requires at least one static partition column. To turn this off set hive.exec.dynamic.partition.mode=nonstrict
修改配置文件的值：
set hive.exec.dynamic.partition.mode=nonstrict;

```

```sql
修改插入语句：
insert into table student_ptn partition(department) select id,name,sex,age,department from student_managed;
按照部门进行分区

-----------------

insert into table student_ptn partition(department) select id,name,department,age,sex from student_managed;
会按照sex 分区 department="男" department="女"

----------------------

默认情况下  select的最后一个字段就是分区字段
注意：动态分区的时候 将分区字段写在查询语句的最后
```

#### 动态和静态分区的区别

- 静态分区插入数据的之后 指定分区的名；动态分区插入数据的时候  不需要指定分区的名  根据数据生成分区的名
- 静态分区中的可能会存在某一个分区没有数据  分区的目录是一个空目录；动态分区根据实际的数据生成的分区  每一个分区至少一条数据  如果一条数据都没有则不会生成这个分区
- 动态分区的时候  每一个分区都会对应配置文件中设置的reducetask的个数（set reducetasks=3）；
  分区过多的时候 产生多个reducetask,拉低性能， 慎重设置reducetask的个数

### 多级分区

> 分区字段大于1个

#### 建表

```
create table if not exists student_ptn01(id int,name string,age int) 
partitioned by(sex string,department string) 
row format delimited fields terminated by ',';

一级分区：sex
二级分区：department
目录结构：
/user/hive/hivehome/bd1807/student_ptn01/sex="男"/department="IS"
```

#### 数据插入

##### load方式

```sql
load data local inpath '/home/hadoop/datas/student.txt' 
into table student_ptn01 
partition(sex="男",department="IS");
```



##### insert的方式(动态）

```sql
insert into table student_ptn01 partition(sex,department) 
select id,name,sex,age,department from student_managed;
这个最后二个字段不是分区字段，查出的数据如果类型不匹配返回null

insert into table student_ptn01 partition(sex,department) 
select id,name,age,sex,department from student_managed;
分区字段  一定在查询的末尾几个字段
```

### 分桶表的数据插入

> **只能用insert**的方式  不能使用load的方式
>
> 分桶----MR中的分区---肯定需要MapReduce程序
>
> load方式不走MR

```sql
测试：
load data local inpath '/home/hadoop/datas/student.txt' into table student_buk;
会报错
```

```
3个桶---MR中的3个分区---3个reducetask任务
insert into table student_buk select * from student_managed;

日志：Hadoop job information for Stage-1: number of mappers: 2; number of reducers: 3

结果：
	/user/hive/hivehome/bd1807.db/student_buk/000000_0
	/user/hive/hivehome/bd1807.db/student_buk/000001_0
	/user/hive/hivehome/bd1807.db/student_buk/000002_0

	分桶字段  id  排序：age  升序
	桶1：000000_0   id---int   id%3=0
	95004   张立    男      19      IS
	95007   易思玲  女      19      MA
	95019   邢小丽  女      19      IS
	95010   孔小涛  男      19      CS
	95001   李勇    男      20      CS
	95022   郑明    男      20      MA
	95013   冯伟    男      21      CS
	123     zss     男      34      IS
	
	桶2：000001_0    id%3=1
	95017   王风娟  女      18      IS
	95011   包小柏  男      18      MA
	95008   李娜    女      18      CS
	95005   刘刚    男      18      MA
	95002   刘晨    女      19      IS
	95014   王小丽  女      19      CS
	95020   赵钱    男      21      IS
	1234    zss     男      34      IS
	
	桶3：000002_0  id%3=2
	95021   周二    男      17      MA
	95015   王君    男      18      MA
	95009   梦圆圆  女      18      MA
	95018   王一    女      19      IS
	95012   孙花    女      20      CS
	95003   王敏    女      22      MA
	95006   孙庆    男      23      CS
	
分桶的字段不是数值   name.hashCode%3

```

> 在hive的1.2版本中   向分桶表插入数据的时候  需要手动设定reducetask的个数的

```tex
In order to change the average load for a reducer (in bytes):
set hive.exec.reducers.bytes.per.reducer=<number>   设置每一个reduvetask的吞吐量
In order to limit the maximum number of reducers:
set hive.exec.reducers.max=<number>   设置的是reducetask的最大的个数  1009
In order to set a constant number of reducers:
set mapreduce.job.reduces=<number>  设置reducetask个数的  -1
-1 根据实际需求  分配reducetask个数
```

# 内部表和外部表区别

```tex
删除内部表的时候  元数据删除  hdfs的原始数据的目录也会删除
删除外部表   元数据被删除了    hdfs的原始数据目录没有删除的

1）数据删除的时候
内部表的数据  表数据自己管理   删除表数据的时候 元数据和原始数据一同删除
外部表数据  hdfs管理的  删除表的时候只会删除元数据信息  原始数据不会被删除
2）应用场景上
内部表通常用于存储一些中间结果数据  （只针对某一个业务模块）
外部表 通常用于一些共享数据  公用数据  原始数据 删除表的时候不会把数据删除
3）建表目录上
内部表建表的时候 一般使用默认的目录就可以
外部表数据一般已经存在hdfs的某一个目录上了  建表的时候一般指定数据目录 location  指定到数据所在目录  这个时候外部表和共享数据就会自动关联
```

# 数据导出

## 单重导出

> 导出到本地文件系统
>
> `INSERT OVERWRITE LOCAL DIRECTORY directory1 select_statement`

```
hive> insert overwrite local directory '/home/wyp/wyp'
    > select * from wyp;
    
将会在本地文件系统的/home/wyp/wyp文件夹下生成文件，这个文件是Reduce产生的结果（这里生成的文件名称是000000_0
95013冯伟男21CS
95020赵钱男21IS
95003王敏女22MA
95006孙庆男23CS

保存的文件列之间是用^A（\x01）来切割

如今我们能够用Hive0.11.0版本号新引进了一个新的特性，指定输出结果列之间的分隔符： 
hive> insert overwrite local directory '/home/wyp/Documents/result'
hive> row format delimited
hive> fields terminated by '\t'
hive> select * from test;
```

## 导出到HDFS中

> `INSERT OVERWRITE DIRECTORY directory1 select_statement`

```
hive> insert overwrite directory '/home/wyp/hdfs'
    > select * from wyp;
    
将会在HDFS的/home/wyp/hdfs文件夹下保存导出来的数据。
```



## 多重导出

> from table  `INSERT OVERWRITE [LOCAL] DIRECTORY directory1 select... where...`

```sql
from student_managed
insert overwrite local directory "/home/sun/datas/age_20" select * where age>20
insert overwrite local directory "/home/sun/datas/agexiao20" select * where age<20;
```

## 导出到Hive的还有一个表中

```
hive> insert into table test
    > partition (age='25')
    > select id, name, tel
    > from wyp;
#####################################################################
           这里输出了一堆Mapreduce任务信息。这里省略
#####################################################################
Total MapReduce CPU Time Spent: 1 seconds 310 msec
OK
Time taken: 19.125 seconds

hive> select * from test;
OK
5       wyp1    131212121212    25
6       wyp2    134535353535    25
7       wyp3    132453535353    25
8       wyp4    154243434355    25
1       wyp     13188888888888  25
2       test    13888888888888  25
3       zs      899314121       25
Time taken: 0.126 seconds, Fetched: 7 row(s)
```


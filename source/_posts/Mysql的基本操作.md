---
title: Mysql的基本操作
tags:
  - mysql
  - null
date: 2018-08-12 16:52:30
categories: Java
top:
---

1. 数据保存到内存：
	- 优点:
		1. 读写速度快
	- 缺点:
		1. 程序关闭导致数据丢失
1. 数据保存到文件：
	- 优点:
		1. 数据可以永久保存
	- 缺点:
		1. 频繁地IO操作，效率不高
		2. 数据管理不方便。例如查询某个数据需要全部读取出来，再匹配。
1. 数据保存到数据库软件：
	- 优点:
		1. 数据永久保存下来
		2. 数据管理非常方便。（例如查询非常快速和方便）

<!-- more -->

# 数据库管理
1. 查询所有的数据库
	- `showe databases ;`
1. 创建数据库
	- `create databases dbtest;`
	- `create databases dbtest CHARACTER SET utf8 COLLATE utf8_general_ci;`
	- `create database dbtest CHARACTER SET GBK COLLATE gbk_chinese_ci;`
1. 修改数据库的字符集
	- `alter database dbtest CHARACTER SET GBK COLLATE gbk_chinese_ci;`
	- `alter database dbtest CHARACTER SET utf8 COLLATE utf8_general_ci;`
1. 删除数据库
	- `drop database dbtest;`
1. 查看所有的字符编码
	- `SHOW CHARACTER SET;`
1. 查看创建数据库的指令并查看数据库使用的编码`
	- `show create database dbtest;`

# 表管理
1. 查看所有表
	- `show tables;`
1. 创建表
	```
	create table tbtest(
		id int(10) auto_increment,
		user_name varchar(60) CHARACTER SET GBK COLLATE gbk_chinese_ci,
		email varchar(60),
		PRIMARY key(id)
	)CHARACTER SET utf8 COLLATE utf8_general_ci;
	```
1. 查看表的结构
	- `desc tbtest;`
1. 删除表
	- `drop table tbtest;`
	
1. 修改表
	1. 添加字段
		1. `alter table student add column sgender varchar(2);`
	2. 删除字段
		1. `alter table student drop column sgender varchar(2);`
	2. 修改字段类型
		1. `alter table student modify column sgender varchar(2);`
	2. 修改字段名称
		1. `alter table student change column sgender varchar(2);`
	2. 修改表的名称
		1. `alter table student rename to teacher;`


# 增删改数据
1. 增加数据
	- 插入所有字段。一定依次按顺序插入
		- `INSERT INTO student VALUES(1,'张三','男',20);`
	- 注意不能少或多字段值
		- `INSERT INTO student VALUES(2,'李四','女');`//报错
	- 插入部分字段
		- `INSERT INTO student(id,NAME) VALUES(2,'李四');`

1. 修改数据
	- 修改所有数据（建议少用）
		`UPDATE student SET gender='女';`
	- 带条件的修改（推荐使用）
		`UPDATE student SET gender='男' WHERE id=1;` -- 修改id为1的学生，修改性别为男
	- 修改多个字段,注意: SET 字段名=值,字段名=值,....
		`UPDATE student SET gender='男',age=30 WHERE id=2;`
1. 删除数据
	- 删除所有数据（建议少用）
		`DELETE FROM student;`
	- 带条件的删除(推荐使用)
		`DELETE FROM student WHERE id=2;`
	-  另一种方式
		- delete from: 可以全表删除      
			- 1)可以带条件删除  
			- 2）只能删除表的数据，不能删除表的约束     
			- 3)使用delete from删除的数据可以回滚（事务）
		- truncate table: 可以全表删除   
			- 1）不能带条件删除 
			- 2）即可以删除表的数据，也可以删除表的约束 
			- 3）使用truncate table删除的数据不能回滚
			- `TRUNCATE TABLE student;`

# 查询数据
## 查询所有列
`SELECT * FROM student;`

## 查询指定列
`SELECT id,NAME,gender FROM student;`

## 查询时添加常量列
`SELECT id,NAME,gender,age,'常亮列' AS '年级'  FROM student;`

## 查询时合并列
> 需求： 查询每个学生的servlet和jsp的总成绩
```
SELECT id,NAME,(servlet+jsp) AS '总成绩' FROM student;
```
- 注意：合并列只能合并数值类型的字段
	- `SELECT id,(NAME+servlet) FROM student;`//报错

## 查询时去除重复值
> 需求： 查询学生的性别     男 女
1. `SELECT DISTINCT gender FROM student;`
2. `SELECT DISTINCT(gender) FROM student;`

## 条件查询(where)
### 逻辑条件
1. and(与)
2. or(或)

> 需求: 查询id 为2 ,且姓名为李四的人
`select * from student where sid =2 and sname = '李四';`-- 交集

> 需求： 查询id为2，或姓名为张三的学生
`SELECT * FROM student WHERE id=2 OR NAME='张三';` -- 并集

### 比较条件:
1. \>
2. <
3. \>=
4. <=
5. =
5. <>(不等于)
6. between and(等价于>= and <=)

> 需求： 查询servlet成绩大于70分的学生
> `SELECT * FROM student WHERE jsp>=75 AND jsp<=90;`
> `SELECT * FROM student WHERE jsp BETWEEN 75 AND 90;` -- (包前包后)

> 需求: 查询性别为女的学生
> `SELECT * FROM student WHERE gender<>'男';`

### 判空(判断null 和 空字符串)
> null 和  空字符串的区别
1. null：表示没有值
1. 空字符串：有值的！

#### 判断null
1. is null
2. is not null

#### 判断空字符串
1. =''
2. \<>''

> 需求： 查询地址为空的学生（包括null和空字符串）
> `SELECT * FROM student WHERE address IS NULL OR address='';` -- （包括null和空字符串)

> 需求： 查询有地址的学生(不包括null和空字符串)
> `SELECT * FROM student WHERE address IS NOT NULL AND address<>'';`

### 模糊条件查询like
1. % : 表示任意个字符
2. _ :(下划线)表示一个字符

> 需求： 查询姓‘张’的学生
> `SELECT * FROM student WHERE NAME LIKE '李%';`

> 需求： 查询姓‘李’，且姓名只有两个字的学生
> `select * from studetn where name like '李__'`

### 聚合查询
常用聚合函数
1. sum()
2. avg()
3. max()
4. min()
5. count()

> 需求：查询学生的servlet的总成绩 (sum() :求和函数)
> `SELECT SUM(servlet) AS 'servlet的总成绩' FROM student;`

> 需求： 查询学生的servlet的平均分
`SELECT AVG(servlet) AS 'servlet的平均分' FROM student;`

> 需求: 查询当前servlet最高分
`SELECT MAX(servlet) AS '最高分' FROM student;`

> 需求： 查询最低分
`SELECT MIN(servlet) AS '最低分' FROM student;`

> 需求： 统计当前有多少学生(count(字段))
`SELECT COUNT(*) FROM student;`
`SELECT COUNT(id) FROM student;`

注意:
1. **count() 函数统计的数量不包含null**
2. **使用count统计表的记录数，要使用不包含null值的字段**


## 分页查询（limit 起始行,查询几行）
1. 起始行从0开始
2. 分页：当前页  每页显示多少条
3. 分页查询当前页的数据的sql: `SELECT * FROM student LIMIT (当前页-1)*每页显示多少条,每页显示多少条`;

> 需求： 查询第1,2条记录（第1页的数据）
> `SELECT * FROM student LIMIT 0,2;`

> 查询第3,4条记录（第2页的数据）
`SELECT * FROM student LIMIT 2,2;`

> 查询第5,6条记录（第3页的数据）
`SELECT * FROM student LIMIT 4,2;`

> 查询第7,8条记录 (没有记录不显示)
`SELECT * FROM student LIMIT 6,2;`

## 查询排序(order by)
语法 ：order by 字段 asc/desc
1. asc: 顺序，正序。数值：递增，字母：自然顺序（a-z）
2. desc: 倒序，反序。数值：递减，字母：自然反序(z-a)
3. 默认情况下，按照插入记录顺序排序

> 需求： 按照id顺序排序
> `SELECT * FROM student ORDER BY id ASC;`
`SELECT * FROM student ORDER BY id; `-- 默认正序
`SELECT * FROM student ORDER BY id DESC;`-- 反序

注意：多个排序条件
> 需求： 按照servlet正序，按照jsp的倒序
`SELECT * FROM student ORDER BY servlet ASC,jsp DESC;`

## 分组查询(group by)
> 需求： 查询男女的人数
> 分组依据要显示出来
```
预期结果：
		男   3
		女   2

1) 把学生按照性别分组(GROUP BY gender)
2) 统计每组的人数(COUNT(*))
```

`select ssex count(*) from group by ssex;`

## 分组查询后筛选(having)
> 需求： 查询总人数大于2的性别

```
 1) 查询男女的人数
 2）筛选出人数大于2的记录(having)
 注意： 分组之前条件使用where关键字，分组之前条件使用having关键字
	SELECT gender,COUNT(*) FROM student WHERE sid> 2 GROUP BY gender HAVING COUNT(*)>2;

```
> <s>SELECT gender,COUNT(*) FROM student GROUP BY gender where COUNT(*)>2;</s>
> 这里分组之后不能使用where
`SELECT gender,COUNT(*) FROM student GROUP BY gender HAVING COUNT(*)>2;`


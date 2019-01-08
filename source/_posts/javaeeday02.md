---
title: javaeeday02
tags:
  - null
  - null
date: 2018-08-24 09:03:04
categories:
top:
---



# 数据的完整性

1. 实体完整性
2. 引用完整性
3. dml
4. 修改和删除
5. 查询
6. 单行函数
7. 聚合函数和分组函数
8. 加密函数

<!-- more -->

# 完整性约束

## 实体完整性

> 保证数据记录之间是准确的(能够唯一标识一个实体)

1. 实体: 客观存在的实物,数据库中指的是一条记录.

> **保证实体的完整性:**
>
> 1. 主键约束
> 2. 唯一约束
> 3. 主键自增约束

### 主键约束()

1. 唯一
2. 非空

#### 主键字段的选择

1. 必须是唯一的
2. 不要选择业务字段(有真实含义的字段(身份证))
3. 一张表中只能有一个主键,但是可以有联合主键(多个字段组成)
4. 每一张表必选要设置主键

#### 创建表的同时添加主键

```sql
# 创建主键(非空,唯一),字段后面添加时不能添加联合主键
CREATE table studetn(
sid int primary key,
sname VARCHAR(20) 
);

# 创建表的时候,先给字段,后添加约束
create table student(
sid int,
sname varchar(20),
primary key(sid)  # 可以设置联合主键
);
```



#### 表已经创建(alter)

> 表已经创建了,添加约束的时候,需要表中的数据合理,不能空和重复

```sql
# 添加约束(constraint)
# 约束名pk_字段
alter table student add constraint pk_sid PRIMARY key(sid);
```



### 唯一约束(unique)

> 不能重复,可以为空(空只能有一个)

#### 创建表的时候添加

```sql
# 添加唯一约束

create  table sun(
uid int PRIMARY KEY,
uname varchar(20),
card VARCHAR(18) unique
);

create  table sun(
uid int PRIMARY KEY,
uname varchar(20),
card VARCHAR(18),
unique(card)
);

```

#### 表存在

```sql
# 添加唯一约束
create  table sun(
uid int PRIMARY KEY,
uname varchar(20),
card VARCHAR(18)
);

alter table sun add constraint uq_card  UNIQUE(card);
```

### 主键自增长约束(auto_increment)

> 从1开始,每次自身加1(oracle)
>
> 好处:不用关心主键重复问题

```sql
# 主键自增长
create table student(
sid int  primary key auto_increment,
sname VARCHAR(20)
);

alter table student MODIFY sid int auto_increment; 
```

### 删除约束

frm:存储数据的元数据信息(结构,约束,索引)

1. 有主键自增长约束的时候,先删除自增长约束,后才能删除主键

```
# 自增长约束
alter table student MODIFY sid int;
# 主键约束
alter table student drop PRIMARY KEY ;
# 唯一约束
drop index uq_sname on student;

```









## 域完整性

> 域: 字段

> **域的完整性:**
>
> 1. 类型约束
> 2. 非空约束:  不能为空  not null
> 3. 默认值: default 值

### 类型约束,非空,默认值

```sql
# 非空 和 默认值
create table student(
sid int PRIMARY KEY auto_increment,
sname varchar(20) not null,
gender bit(1) default 1
);
```

## 引用完整性

> 一张表中通用列的取值必须参考另外一张表主键字段

### 外键约束(foreign key)

> 主外键关联
>
> add constraint 外键名 foreign key(外键关联字段) references 参考表(参考字段)
>
> 1. 参考字段通常是参考表的主键
> 2. 外键关联字段名称可以不一样,但是类型必须一致

```sql
alter table student add constraint FK_cid foreign key(cid) references classroom(cid);
```



## 自定义完整性

### check约束:在mysql中不能使用

```sql
alter table student add constraint check (sage between 1 and 50)
```



# 运算符

## 算数运算符



```sql
select 1+1;	//2

select 1-1;	//0

select 1*2;	//2

select 3/2;	//1.5

select 3 div 2;  1 ## 整除

select 3/0;	# null

```



## 比较运算符

> 1. \>
> 2. <
> 3. != / <>
> 4. =
> 5. \>=
> 6. <=
> 7. true  显示:1
> 8. false  显示:0
> 9. is  null     //  is  not  null
> 10. between ... and ... [ a , b ]
> 11. in   //  not  in


## 逻辑运算符

1. and
2. or
3. !

```sql
select 1=1 and 1=2;		//0
select 1=1 and 1=2;		//1
select !2=2;		//0
```

## 位运算符

1. &
2. |
3. ^(异或)

```sql
select 3 & 2; # 2
select 3 | 2; # 3
select 3 ^ 2; # 1
```








# DML

## 添加数据insert


> insert into tname[(字段名称....)] values(值...)


1. 默认插入:给所有的字段添加值,和表中的字段顺序一致

   ```sql
   insert into classroom VALUES (3,'bd1807')
   ```

2. 添加指定的字段,cid是自增长的

		给定字段时,值和给定的字段顺序一致

    ```sql
    insert into classroom(cname) values ('bd1808');
    ```

3. 添加多条数据:使用逗号隔开

    ```sql
    insert into classroom(cname) value ('bd1809'),
    ('bd1810'),
    ('bd1811');
    ```

4. 将classroom 的值 整体复制到 classroom1

    ```sql
    # 将classroom 的值 整体复制到 classroom1
    insert into classroom1 select * from 
    
    create  table classroom1 select * from classroom;
    ```

5. 复制表的结构

    ```sql
    create  table classroom1 select * from classroom where 0; 
    ```

## 删除数据delete

> delete是逐行删除,不影响自增长.自是修改数据(自增不会重置)
>
> truncate 清空,文件级别的清空,(自增重置)

1. 删除所有

```sql
delete from classroom;
```

2. 删除指定的记录

```sql
delete from classroom where cid=2;
```

## 修改数据update

> 使用的时候不带条件是对整张表修改
>
> update  表名  set 字段 = 值 where 字段 = 值

```sql
update classroom set cname='1806' where cid =1;
```

## 查询数据

### 普通查询select

> select 字段| 表达式 form 表|视图|结果集
>
> select子句的作用:字段值
>
> from子句: 从哪些表或者结果集中查询
>
> where: 条件

```sql
# 查询
select * from emp;

# 查询所有员工的姓名和工资
select ename,sal from emp;

# 查询工资大于2000
select * from emp where sal>=2000;

# 查询工资1000~2000
select * from emp where sal between 1000 and 2000;
```



### 集合查询(in ([值 , 值 ,...]))

```sql
select * from emp where empno=7521 or `empno`=7369 or empno = 7788;
# 集合查询
select * from emp wher empno in (7521,7369,7788)
```

### 取别名 (as 别名)

> 简化 作解释说明
>
> (as) 别名

```sql
select 1+1 count; # 将结果用count表示

select ename,sal*1.5 new  from emp; # 将结果用new表示

select emp.ename from emp;

select e.sal from emp e;

```

### 去除重复distinct

> 在查询前面添加 distinct

```sql
select DISTINCT job from emp;
```

### 排序order by

> 默认升序:assc
>
> 降序 : desc
>
> 多个规则使用 逗号隔开

```sql
select * from  emp where deptno=10 order BY emp.sal desc;
select * from  emp where deptno=10 order BY emp.sal desc,emp.ename desc;
```

### 限制结果查询limit

> limti : 下标  ,  长度
>
> **仅仅适用于MySQL.**

```sql
# 取5条记录
select * from emp limit 0,5;
# 取部门为10的员工中的最大值
select * from  emp where deptno=10 order BY emp.sal desc limit 0,1;
```

### 模糊查询:like

> % 表示多个字符
>
> _ 表示一个字符

```sql
# 模糊查询
select * from emp where ename like 's%';
select * from emp where ename like'%s'
select * from emp where ename like'%s%'

# 第二个字符为L的所有员工信息
select * from emp where ename like '_l%';
```

# 函数

## 单行函数

### 数学函数

> 返回值只有一条记录

 1. PI() :π
 2. ceil(): 向上取整
 3. floor():向下取整
 4. round():四舍五入
 5. mod(): 取模
 6. rand(): 随机数
 7. pow():幂运算

```sql
select PI();	# 3.141593

select ceil(12.3);# 13
select ceil(-12.3);;#12

select floor(12.3);	#12
select floor(-12.3);	#-13

select round(3.5);	#4
select round(3.45);	#3
select round(3.45,1);	#3.5
select round(3.45,-1);	#0

select mod(5,2);	# 1

select rand();[0,1)
               
# 随机从emp获取二条记录
select * from emp ORDER BY rand() LIMIT 2;

select pow(2,3);  # 2^3=8
```



### 字符函数

1. length(): 获取字符的长度
2. lower('THIS'):字符转为小写字母
3. upper('this'): 字符转为大写字母
4. substr('this is zs' ,  1 , 6): 下标从1开始
5. replace(str,from_str,to_str); ;字符的替换
6. trim('str') : 去除字符二端的空格
7. lpad(str, num , 'str'): 用'str'  左填充str, num小于length,截取
8. rpad(str, num , 'str'):右填充



```sql
select length('this is zs')		# 10

select upper('this')    # THIS

select lower('THIS')   # this

select substr('this is zs',1,6)		#this i


select replace('this is zs','this','he'); #he is zs

select trim(' this is '); #去两端空格 this is

select LPAD('aa',10,'*');   #********aa

select LPAD('ab',1,'*'); 	#a

select rpad('aa',10,'*');   #aa********

select rpad('ab',1,'*'); 	#a
```



### 日期函数

1. now(): 当前时间
2. sysdate:获取系统时间
3. curdate() /current_date(): 获取日期 : 年月日
4. curtime() / current_time(): 获取时间 : 时分秒
5. year(date): 获取指定时间的年份
6. month(date): 获取指定时间的月份
7. day(date): 获取指定是时间的天数
8. **last_day(date):**:获取指点时间的月份的最后一天
9. **date_add(时间 , interval  值  ,类型):**值可以为正负,类型可以是year/month/day

```sql
 select NOW();  #当前时间 2018-08-29 21:17:54
 
 select SYSDATE();  #获取系统时间 2018-08-29 21:18:04
 
 select CURRENT_DATE();	2018-08-29
 
 select CURDATE();	2018-08-29
 
 select CURRENT_TIME();	21:18:47
 
 select CURTIME();	21:18:47
 
 select YEAR('1998-09-09');	1998
 
 select YEAR(NOW());	2018
 
 select MONTH(NOW());	8
 
 select DAY(NOW());	29
 
 #获取当前月最后一天
 select LAST_DAY('2018-02-01');	2018-02-28
 
 #日期计算
 select DATE_ADD(NOW(),interval 2 MONTH);	2018-10-29 21:20:28
```



## 聚合函数

> 对数据整体进行运算,查询语句中**不能出现单个字段**

1. min():最小值
2. max():最大值
3. avg():平均值
4. count():总计
5. sum():求和

> count();常用来统计表中的记录数,但是不会统计字段中为nulll的记录,所以统计表中的记录数的时候,选择不为null的更好,
>
> 分页操作:聚合函数和限制结果查询

```sql
select max(sal) from emp;
select min(sal) from emp;
select avg(sal) from emp;  
select count(*) from emp;  #记录数
select count(1) from emp;  #记录数
select count(comm) from emp; #字段非空总数
select sum(sal) from emp;
```



## 分组函数group by

> 使用分组的依据可以出现在select  查询的语句中

1. where 在group by 之后
2. where 中 不能使用聚合函数

> having

1. having 代替where ,分组之后进行检索
2. 可以使用聚合函数

```sql
#分组 group by 分组条件   having:分组之后进行检索
	  select deptno,avg(sal) from emp group by deptno;
		
# 查询平均工资大于2000的部门的编号和平均工资。
# 1.where在group by之后
# 2.where中不能使用聚合函数
	   select deptno,avg(sal) from emp group by deptno having avg(sal) > 2000;
```





## 加密函数

1. MD5(str):32为位
2. SHA(str):40位
3. password(str):

```sql
select MD5('root'); #63a9f0ea7bb98050796b649e85481845
select SHA('root');#dc76e9f0c0006e8f919e0c515c66dbba3982f785
select password('root');#*81F5E21E35407D884A6CD4A731AEBFB6AF209E1B # 41位
```


---
title: javaeeday03
tags:
  - null
  - null
date: 2018-08-27 09:01:18
categories:
top:
---

1. 连接查询
2. 子查询
3. 联合查询
4. 事务
5. 存储过程
6. 函数
7. 编码问题

<!-- more -->

# 高级查询

## 关联查询（连接查询）

> 查询员工编号为 7788 的员工姓名 和所在的部门的名称

### 内连接

1. select   where 
2. inner join ...on...
3. inner join .... using(通用列（连接字段）)：
   1. 通用列的名称要一样
   2. 不会出现二次通用列（去除重复的字段）

```sql
# 查询员工编号为 7788 的员工姓名 和所在的部门的名称
select empno, ename,dname 
from emp,dept 
where empno=7788 and emp.deptno = dept.deptno

# 内连接
select * FROM emp 
inner join dept
on emp.deptno = dept.deptno

# 内连接
select * from emp
inner join dept
using(deptno)
```

#### 特点

1. 关联表中都出现的字段值最终才能出现在结果集中
2. 内连接与连接顺序无关
3. 内连接不分主从表

### 外连接

> 有主从表之分
>
> 1. 依次遍历主表中的记录，与从表记录进行匹配
> 2. 如果匹配，连接显示
> 3. 如果不匹配，以null 填充

1. 左外连接 ：left[outer] join ...on
   1. 前面是主表，后面是从表
2. 右外连接： right[outer] join ... on
   1. 前面是从表，后面是主表

```sql
# 左外连接: 14
select * from emp
left join dept
on emp.deptno = dept.deptno

# emp 当作主表， dept当作从表
# 依次遍历主表的记录
# 根据条件匹配从表记录
# 显示查询的结果

# 左外连接: 15
select * from dept
left join emp
on emp.deptno = dept.deptno

# dept是主表 ，
# 主表仍要遍历，虽然从表没有数据

```

### 等值连接

#### 自然连接natural join（都是等值连接)



> 不需要声明某个字段之间相等
>
> 是一种特殊的等值连接

1. **自然连接肯定是等值连接**
2. **等值连接不一定是自然连接**

```sql
# 自然连接
select * from emp natural join dept;
```



### 自连接

> 可以将自身表的一个[镜像](https://baike.baidu.com/item/%E9%95%9C%E5%83%8F/1574)当作另一个表来对待，从而能够得到一些特殊的数据。 

```sql
# 查询所有员工和他的上级领导
# 自连接
select e1.ename ,e2.ename from emp e1,emp e2 where e1.mgr = e2.empno

# 外连接
select emp.empno, emp.ename ,e1.ename from emp left join emp e1 on emp.mgr = e1.empno
```

## 子查询（嵌套查询）

> 将一个查询结果当作另外一个查询的条件或表达结果集
>
> 是最接近思考方式，最自然的查询

### 单行子查询

> 子查询的返回结果只有一条记录
>
> 1. \>
> 2. <
> 3. =
> 4. <>
> 5. 等等

```sql
# 查询大于scott 薪水的员工信息
select * from emp 
left join dept
on emp.empno= dept.deptno
where emp.sal> (select sal from emp where ename='SCOTT')
```



### 多行子查询

> 子查询的返回结果有多条记录
>
> 1. in
> 2. any
>    1.   = any ： 相当于 in
>    2.   \> any :  大于最小值
>    3.  < any ： 小于最大值
> 3. all
>    1. \> all: 大于最大值
>    2. <all: 小于最小值

```sql
# 查询员工的工资在20号部门的工资内且不在20号部门
SELECT	* FROM	emp 
WHERE sal IN ( 
	SELECT DISTINCT sal FROM emp WHERE deptno = 20 ) 
AND deptno <> 20
	
# 查询20号部门除了工资最高员工 的 员工信息
select * from emp where sal<any(
	select distinct sal from emp where deptno=20)
and deptno=20
    
# 查询大于20号部门的平均工资的20号部门的员工信息
select * from emp where sal>
	(select avg(sal) from emp where deptno =20)
and deptno =20
```

```sql
	# 查询大于所在部门的平均工资的员工信息

SELECT	* FROM	emp 
WHERE	sal > ( 
SELECT avg( sal ) FROM emp e2 GROUP BY deptno HAVING emp.deptno = e2.deptno ) ;

1. 主查询遍历整个emp表
2. 主查询读取某一条记录的deptno值，将该值交给子查询
3. 子查询根据主查询的传来的deptno值，查询出指定部门的平均工资然后将整体结果返回给主查询
4. 主查询根据子查询的结果最终执行。

```



```sql
# 查询工资> 20号部门的所有员工的信息
select * from emp where sal>(
select max(sal) from emp where deptno=20);

select * from emp where sal>all(
select sal from emp where deptno=20);
```



### exists

```sql
# 查询薪水> 2000 的员工所在的部门信息
# 关联查询
	select distinct dept.* from emp,dept 
	where emp.deptno = dept.deptno and sal> 2000

# 子查询
	select * from dept where deptno in
	(select distinct deptno from emp where sal> 2000);

# exists
# 子查询的时候，主查询条件字段和子查询的返回结果字段必须意义对应
	select * from dept where exists (
	select * from emp where sal>2000 and dept.deptno= emp.deptno)

```



**exists 和 in的区别**

1. in 先执行子查询，将结果返回给主查询，主查询继续执行
2. exists :先执行主查询，将主查询的值依次子啊子查询中进行匹配，**根据是否匹配返回韬略或者false,如果是true就连接展示否则不展示**，
3. exists :实际上是主查询传值给子查询

## 子查询和多表

1. 子查询--> 查询条件和结果放在一张表
   1. 结果分布于多张表
2. 关联查询: 能够处理结果放在一张表,但是占内存

## 联合查询(索引)

> 索引的时候 ,使用or会导致索引失败
>
> **联合会的结果集必须一致**

1. union: 去重
2. union all

```sql
# 查询20号部门 以及工资>2000的员工信息
# union all 8条
select * from emp where deptno=20 or sal>2000

# union all 8条
select * from emp where deptno=20
union 
select * from emp where sal>2000

# union all 11条
select * from emp where deptno=20
union all
select * from emp where sal>2000
```



# 事务

存储引擎

> 数据库底层软件组织,DBMS通过存储引擎实现对数据库的操作;**MySQL的核心就是存储引擎.**

> MySQL中可以设置多种存储引擎,不同的存储引擎在索引,存储,以及锁策略上是不同的

- MySQL5.5之前,采用myisam存储引擎,支持全文搜索,不支持事务
- mysql5.5之后,默认采用innidb存储引擎,支持事务以及行锁定

## 什么是事务

> 事务保证数据一致性,一组DML操作要么同时成功,要么同时失败



## 事务的特性

```sql
# 张三 ---> 李四   ---->100
update account set money =money -100 where name='zs';
update account set money =money +100 where name='ls';

update account set money =money -100 where name='zs';
delete form aa;
## 出现错误,后面执行失败
update account set money =money +100 where name='ls';
```



> 事务的acid特性

1. 原子性:放在同一个事务中的一组操作是不可分割的.
2. 一致性:在事务的执行前后,总体的状态保持不变
3. 隔离性:并发事务之间互相不能干扰
4. 持久性:事务执行之后,将永久化到数据库



## 事务的语法

> 一组DML操作,放到同一个事务中进行处理

> mysql 数据库采用自动事务提交: 
>
> 	每次执行一个DML操作,系统会自动创建一个事务,并且执行后,自动提交

- 查看MySQL的事务:自动提交

  - show variables like 'autocommit';

- 关闭MySQL的自动提交

  - set autocommit = 0;
  - set autocommit = 1; 

  

  1. 显式开启事务(可以不写)
     - start transaction;
     - begin
  2. 提交事务:
     - commit;
  3. 回滚事务

```sql
1. 开启事务: DML操作隐式开启事务
2. 一组DML操作
3. 提交/回滚事务
```



```sql
# 显示开启事务
Start transaction;
update account set money =money -100 where name='zs';
update account set money =money +100 where name='ls';

# 手动提交
commit;
# 手动回滚
rollback;
```



## 并发事务产生的问题

1. 脏读:在一个事务的执行范围内读到了另一事务未提交的数据
2. 不可重复读:一个事务在只读范围内,被另一个事务修改并提交事务,导致多次读取事务不一致的问题.
3. 幻读(虚读):一个事务只读范围内,被另一个事务删除 或者添加数据,导致数据读取不一致.

## 事务的隔离级别

1. 读未提交:不能处理任何问题
2. 读已经提交: 只能读到一个事务提交后的数据问题,**解决脏读**
3. 可重复读: **解决脏读问题和不可从重复读问题**,**MySQL默认**
4. 串行化:**可以解决所有问题,执行效率低**

- 参看事务的级别
- 

# 存储程序

> 一组存储和执行的在服务器端的程序
>
> 运行于服务器端

1. 优点:
   - 简化开发
   - 执行效率高(不需要校验和验证)
2. 缺点:
   - 程序保持在服务器端,占用服务器资源
   - 数据迁移麻烦
   - 调试,编写程序不方便

## 分类

### 存储过程

> 服务器端运行的,可重复调用的sql代码块,
>
> 组成:名称,输入输出参数以及一组sql

#### 创建

1. 无参数:

```sql
delimiter // # 设置//为结束标志,分号就不是结束标志了
create procedure sel_emp()
BEGIN
select ename,dname from emp,dept where emp.deptno=dept.deptno;
end //

# 使用
call sel_emp();
```



2. 传入模式(默认为 in)

   > (模式 参数名 类型)

```sql
# 根据编号查询员工
delimiter //
create PROCEDURE  findEmpByNo(eno int)
BEGIN
select * from emp where deptno = eno;
end;

call findEmpByNo(10);

```

3. 输出模式 

   >  out: 定义一个输出变量
   >
   >  into:将结果赋值给变量

   ```sql
   # 根据员工编号查询员工姓名
   delimiter //;
   create PROCEDURE findNameByNO(eno int ,out v_name varchar(20))
   BEGIN
   SELECT ename into v_name from  emp where empno = eno;
   END
   
   # 使用
   call findNameByNO(7788,@name);
   select @name;
   ```

4. 输入输出模式

   ```sql
   # 根据员工姓名查询员工职位
   delimiter //;
   create PROCEDURE findJob (inout name_job VARCHAR(20))
   BEGIN
   select job into name_job FROM emp where ename = name_job;
   end;
   
   # 使用
   set @name_job = 'smmith';
   call findJob(@name_job);
   select @name_job;
   ```

####  控制语句

1. if

```sql
# 控制语句
# 成绩分级
delimiter //;
create PROCEDURE score_lever1( score int)
BEGIN
	declare v_level varchar(20);# 变量的声明
	if score > 80 then 
		set v_level = 'A';   # 变量的赋值
	elseif score>=60 then
		set v_level = 'B';
	else
		set v_level = 'c';
	end if;
	select v_level;
end;

call score_lever1(90); # A
```



1. 循环
   1. while 条件 do... end while
   2. loop.... end loop(死循环);  判断: leave
   3. repeat  until ...   end repeat;(死循环)



### 存储函数

> 存储在服务器端,有返回值,函数作为sql一部分使用



### 函数和存储过程的区别

1. 关键字不同
2. 存储过程三种参数模式实现数据输入输出;函数有返回值

### 触发器

1. 不要添加过多出发器降效率
2. 存储程序中不能使用事务控制

```sql
# 创建触发器
delimiter //;
create trigger tri_user
after delete
on userinfo for each row
begin
# old new
insert into user_bak values(old.uid,old.uname,old.pwd);
end;

delete from userinfo where uid=1;
```


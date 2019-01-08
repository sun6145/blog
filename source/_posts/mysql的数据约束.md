---
title: mysql的数据约束
tags:
  - mysql的数据约束
  - null
date: 2018-08-12 21:45:07
categories: Java
top:
---
# SQL语句的分类：		
- DDL： 数据定义语言
	- create / drop / alter 	
- DML：数据操作语句
	- insert / delete /update / truncate 	
- DQL： 数据查询语言：
	- select / show 		

<!-- more -->

# 数据约束
> 对用户操作表的数据进行约束

## 默认值(default)
> 作用:当用户对使用默认值的字段**不插入**值的时候，就使用默认值。

注意:
1. 对默认值字段插入null是可以的。
2. 对默认值字段可以插入非null

```
CREATE TABLE student(
	id INT,
	NAME VARCHAR(20),
	address VARCHAR(20) DEFAULT '广州天河'  -- 默认值
)

当字段没有插入值的时候，mysql自动给该字段分配默认值
INSERT INTO student(id,NAME) VALUES(1,'张三');

注意：默认值的字段允许为null
INSERT INTO student(id,NAME,address) VALUE(2,'李四',NULL);
INSERT INTO student(id,NAME,address) VALUE(3,'王五','广州番禺');
```

## 非空(not null)
> 作用： 限制字段必须赋值

注意：
1. 非空字符必须赋值
2. 非空字符不能赋null

```
CREATE TABLE student(
	id INT,
	NAME VARCHAR(20),
	gender VARCHAR(2) NOT NULL -- 非空
)

-- 非空字段必须赋值
INSERT INTO student(id,NAME) VALUES(1,'李四');

-- 非空字符不能插入null
INSERT INTO student(id,NAME,gender) VALUES(1,'李四',NULL);	//出错

```

## 唯一(unique)
> 作用： 对字段的值不能重复
		
注意：
1. 唯一字段可以插入null				
2. 唯一字段可以插入多个null
		
```
CREATE TABLE student(
	id INT UNIQUE, -- 唯一
	NAME VARCHAR(20)
)

INSERT INTO student(id,NAME) VALUES(1,'zs');
INSERT INTO student(id,NAME) VALUES(1,'lisi'); -- ERROR 1062 (23000): Duplicate entry '1' for key 'id'

INSERT INTO student(id,NAME) VALUES(2,'lisi');

```

## 主键(非空,唯一)
> 作用： 非空+唯一
			
注意：			
1. 通常情况下，**每张表都会设置一个主键字段**。用于标记表中的每条记录的唯一性。
2. 建议不要选择表的包含业务含义的字段作为主键，建议给每张表独立设计一个非业务含义的id字段。

```
CREATE TABLE student(
	id INT PRIMARY KEY, -- 主键
	NAME VARCHAR(20)
)

INSERT INTO student(id,NAME) VALUES(1,'张三');
INSERT INTO student(id,NAME) VALUES(2,'张三');
-- INSERT INTO student(id,NAME) VALUES(1,'李四'); -- 违反唯一约束： Duplicate entry '1' for key 'PRIMARY'

-- insert into student(name) value('李四'); -- 违反非空约束： ERROR 1048 (23000): Column 'id' cannot be null

```

## 自增长(auto_increment)
> 自动递增

- id int(4): 设置长度为4位
	`insert into student(NAME) VALUES('张三');`	结果: id 0001

```
CREATE TABLE student(
	id INT(4) ZEROFILL PRIMARY KEY AUTO_INCREMENT, -- 自增长，从0开始递增,第一条为1  ZEROFILL 零填充
	NAME VARCHAR(20)
)

-- 自增长字段可以不赋值，自动递增
INSERT INTO student(NAME) VALUES('张三');
INSERT INTO student(NAME) VALUES('李四');
INSERT INTO student(NAME) VALUES('王五');

-- 删除后自增长的区别
	-- delete不能影响自增长约束,从上次删除的数开始
	DELETE FROM student;

	-- 可以影响自增长约束,从0开始
	TRUNCATE TABLE student;
```

## 外键 
> constraint 约束名 foreign key  (外键) references (主键)
> 约束二张表的数据

> 例如： 员工表  和  部门表
		问题出现：在插入员工表数据的时候，员工表的部门ID字段可以随便插入！！！！！	

出现两种表的情况
1. 解决数据冗余高问题： 独立出一张表(1万员工就1万部门,冗余度高)
2. 使用外键约束：约束插入员工表的部门ID字段值

```
-- 部门表（主表）
CREATE TABLE dept(
	id INT PRIMARY KEY,
	deptName VARCHAR(20)
)

-- 修改员工表（副表/从表）
CREATE TABLE employee(
	id INT PRIMARY KEY,
	empName VARCHAR(20),
	deptId INT,-- 把部门名称改为部门ID
	-- 声明一个外键约束
	CONSTRAINT emlyee_dept_fk FOREIGN KEY(deptId) REFERENCES dept(id)
	--           外键名称                  外键               参考表(参考字段)
)

```

注意：
1. 被约束的表称为副表，约束别人的表称为主表，外键设置在副表上的！！！
1. 主表的参考字段通用为主键！
1. 添加数据： 先添加主表，再添加副表
1. 修改数据： 先修改副表，再修改主表
1. 删除数据： 先删除副表，再删除主表

```
INSERT INTO dept(id,deptName) VALUES(1,'软件开发部');
INSERT INTO dept(id,deptName) VALUES(2,'应用维护部');
INSERT INTO dept(id,deptName) VALUES(3,'秘书部');

INSERT INTO employee VALUES(1,'张三',1);
INSERT INTO employee VALUES(2,'李四',1);
INSERT INTO employee VALUES(3,'王五',2);
INSERT INTO employee VALUES(4,'陈六',3);

-- 1）当有了外键约束，添加数据的顺序： 先添加主表，再添加副表数据

员工插入了一个不存在的部门数据
INSERT INTO employee VALUES(5,'陈六',4);
-- 违反外键约束： Cannot add or update a child row: a foreign key constraint fails (`day16`.`employee`, CONSTRAINT `emlyee_dept_fk` FOREIGN KEY (`deptId`) REFERENCES `dept` (`id`))

-- 2）当有了外键约束，修改数据的顺序： 先修改副表，再修改主表数据
//确认副标不存在主表的关联数据 ,才能删除主表数据
UPDATE dept SET id=4 WHERE id=3;//不能修改
UPDATE employee SET deptId=2 WHERE id=4;

-- 3）当有了外键约束，删除数据的顺序： 先删除副表，再删除主表数据
//确认副标不存在主表的关联数据 ,才能删除主表数据
DELETE FROM dept WHERE id=2;	//不能删除
DELETE FROM employee WHERE deptId=2;	//删除副表
```

## 级联操作 on update cascade
>  当有了外键约束的时候，必须先修改或删除副表中的所有关联数据，才能修改或删除主表！但是，我们希望直接修改或删除主表数据，从而影响副表数据。可以使用级联操作实现！！！

- 级联修改： ON UPDATE CASCADE
- 级联删除： ON DELETE CASCADE
- 联操作必须在外键基础上使用

```
CREATE TABLE employee(
	id INT PRIMARY KEY,
	empName VARCHAR(20),
	deptId INT,-- 把部门名称改为部门ID
	-- 声明一个外键约束
	CONSTRAINT emlyee_dept_fk FOREIGN KEY(deptId) REFERENCES dept(id) ON UPDATE CASCADE ON DELETE CASCADE  -- ON CASCADE UPDATE ：级联修改
	--           外键名称                  外键               参考表(参考字段)
)

```

```
-- 级联修改（修改）
-- 直接修改部门
UPDATE dept SET id=5 WHERE id=4;	//属于这个部门的员工的部门id也修改了

-- 级联删除
-- 直接删除部门 
DELETE FROM dept WHERE id=1;	//属于这个部门的员工也删除了

```
---
title: Mysql数据库设计
tags:
  - 三大范式
  - 多表查询
  - 表约束
  - 表连接
  - 触发器
  - 存储过程
date: 2018-08-13 09:37:06
categories: Java
top:
---

需求设计

1. 概要设计：
	- 抽取实体：业务模型 -> 实体模型（java 类 c++类）内存
		- class Book{ name, bookNo,author }
	- 数据库设计:
		- 业务模型/实体模型 - > 数据模型 (硬盘)
			
		- 数据库表设计
2. 详细设计
	类详细，属性和方法

<!-- more -->

# 三大范式
> 设计原则： 建议设计的表尽量遵守三大范式。

## 第一范式： 要求表的每个字段必须是不可分割的独立单元
```
student     :   name              -- (大名小名在一起)违反第一范式
			  张小名|狗娃					
sutdent    ： name    old_name    --符合第一范式
			张小名    狗娃
```

## 第二范式： 在第一范式的基础上，要求每张表只表达一个意思。表的每个字段都和表的主键有依赖。
```					
employee（员工）: 员工编号  员工姓名 部门名称   订单名称  --违反第二范式(订单和编号没有依赖关系)

员工表：员工编号  员工姓名 部门名称   

订单表：  订单编号  订单名称             -- 符合第二范式
```

## 第三范式： 在第二范式基础，要求每张表的主键之外的其他字段都只能和主键有直接决定依赖关系。

```
员工表： 员工编号（主键） 员工姓名  部门编号  部门名 --符合第二范式，违反第三范式																	（数据冗余高）

员工表：员工编号（主键） 员工姓名  部门编号    --符合第三范式（降低数据冗余）
部门表：部门编号  部门名
```

#  MySQL常用数据类型
|分类|数据类型|说明|
|:---:|:---|---:|
|数值类型|BIT(M)  bit  bit(8)|位类型。M指定位数，默认值1，范围1-64|
|数值类型|TINYINT [UNSIGNED] [ZEROFILL]  byte|带符号的范围是-128到127。无符号0到255。|
|数值类型|BOOL，BOOLEAN   boolean|使用0或1表示真或假|
|数值类型|SMALLINT [UNSIGNED] [ZEROFILL] short|2的16次方|
|数值类型|INT [UNSIGNED] [ZEROFILL] int|2的32次方|
|数值类型|BIGINT [UNSIGNED] [ZEROFILL] long|2的64次方|
|数值类型|FLOAT[(M,D)] [UNSIGNED] [ZEROFILL]  float|M指定显示长度，d指定小数位数|
|数值类型|DOUBLE[(M,D)] [UNSIGNED] [ZEROFILL] double|表示比float精度更大的小数|
|文本、二进制类型|CHAR(size) char(20)  max—255   abc  |固定长度字符串|
|文本、二进制类型|VARCHAR(size)  varchar(20) 65535   abcde String|可变长度字符串|
|文本、二进制类型|BLOB   LONGBLOB  大的二进制数据|二进制文本数据|
|文本、二进制类型|TEXT(clob)          LONGTEXT(longclob)  大文本数据|大文本|
|时间日期|DATE/DATETIME/TimeStamp|日期类型(YYYY-MM-DD)  (YYYY-MM-DD HH:MM:SS)，TimeStamp表示时间戳，它可用于自动记录insert、update操作的时间|

> **VARCHAR、BLOB和TEXT类是变长类型。每个类型的存储需求取决于列值的实际长度。 
**

# mysql中文乱码

```
mysql有六处使用了字符集，分别为：client 、connection、database、results、server 、system。
client是客户端使用的字符集。 
connection是连接数据库的字符集设置类型，如果程序没有指明连接数据库使用的字符集类型就按照服务器端默认的字符集设置。       
database是数据库服务器中某个库使用的字符集设定，如果建库时没有指明，将使用服务器安装时指定的字符集设置。    
results是数据库给客户端返回时使用的字符集设定，如果没有指明，使用服务器默认的字符集。       
server是服务器安装时指定的默认字符集设定。       
system是数据库系统使用的字符集设定。

查看和修改数据库字符集
show variables like 'character%';
set character_set_results=gbk;
set character_set_client=gbk; 

```

# 多表查询(关联查询)

## 交叉连接查询
```
-- 需求：查询员工及其所在部门(显示员工姓名，部门名称)
-- 2.1 交叉连接查询（不推荐。产生笛卡尔乘积现象：4 * 4=16，有些是重复记录）
SELECT empName,deptName FROM employee,dept;
```
## 内连接查询
```
-- 内连接的另一种语法
select 内容 
from 表
inner join 表
on 二表之间的联系
```

```
-- 需求：查询员工及其所在部门(显示员工姓名，部门名称)
-- 多表查询规则：
	1）确定查询哪些表   
	2）确定哪些哪些字段   
	3）表与表之间连接条件 (规律：连接条件数量是表数量-1)
-- 2.2 内连接查询：只有满足条件的结果才会显示(使用最频繁)

SELECT empName,deptName       -- 2）确定哪些哪些字段
	FROM employee,dept    -- 1）确定查询哪些表
	WHERE employee.deptId=dept.id  -- 3）表与表之间连接条件
	
-- 内连接的另一种语法
SELECT empName,deptName
	FROM employee
	INNER JOIN dept
	ON employee.deptId=dept.id;

-- 使用表的别名
SELECT e.empName,d.deptName
	FROM employee e
	INNER JOIN dept d
	ON e.deptId=d.id;
```
## 外连接
### 左[外]连接查询： 
>使用左边表的数据去匹配右边表的数据，如果符合连接条件的结果则显示，如果不符合连接条件则显示null

- (注意： 左外连接：左表的数据一定会完成显示！）

  -- 需求： 查询每个部门的员工
  -- 预期结果：
   --  软件开发部  张三
   --  软件开发部  李四
   --  应用维护部  王五
   --  秘书部      陈六
   --  总经办      null 

```mysql
SELECT d.deptName,e.empName

	FROM dept d

	LEFT OUTER JOIN employee e

	ON d.id=e.deptId;

```

### 右[外]连接查询: 
> 使用右边表的数据去匹配左边表的数据，如果符合连接条件的结果则显示，如果不符合连接条件则显示null

- （注意： 右外连接：右表的数据一定会完成显示！）

```
SELECT d.deptName,e.empName
	FROM employee e
	RIGHT OUTER JOIN dept d
	ON d.id=e.deptId;
```

## 自连接查询
```
-- 需求：查询员工及其上司
-- 预期结果：       
	-- 张三    null
	-- 李四    张三
	-- 王五    李四
	-- 陈六    王五
SELECT e.empName,b.empName
	FROM employee e 
	LEFT OUTER JOIN employee b
	ON e.bossId=b.id;
```

# 存储过程
> 存储过程是**带有逻辑的sql语句**
> 之前的sql语句没有条件判断,也没有循环
> 存储过程带上流程控制语句(if,while)

## 特点:
1. 执行速度非常快!存储过程是在数据库的服务器端执行的
2. 移植性很差,不同数据库的存储过程是不能移植的

## 语法
### 创建存储过程
```
delimiter $ --声明存储过程的结束符
create procedure pro_test()	--存储过程名称(参数列表)
begin	-- 开始
	/////////////////////////////
	////  SQL 语句 + 流程控制语句//
	/////////////////////////////
end $
```

### 执行存储过程
> `call pro_test`    --call  存储过程名称(参数)

### 存储过程的参数
1. in :表示输入参数,可以携带数据到存储过程中
2. out: 表示输出参数,可以从存储过程中返回结果
3. inout : 表示输入输出参数,既可以输入功能, 也可以输出功能

## 带有输入参数的存储过程

```
需求: 传入一个员工的id,查询员工信息
delimiter $
create procedure pro_findById(in eid int)   -- in : 输入参数
begin
	select * from employee where id = eid;
	End $

// 调用  ////////
call pro_findById(4);
```

## 带有输出参数的存储过程
```
delimiter $ 
create procedure pro_testOut(Out str varchar(20)) --out: 输出参数
begin
	-- 给参数赋值
	set str = "hello java";
End $

使用:
call pro_testOut(@name);
selec @name
```

### 接收返回参数的值
#### mysql的变量
1. 全局变量

```
全局变量（内置变量）：mysql数据库内置的变量 （所有连接都起作用）
-- 查看所有全局变量： show variables
-- 查看某个全局变量： select @@变量名
-- 修改全局变量： set 变量名=新值
-- character_set_client: mysql服务器的接收数据的编码
-- character_set_results：mysql服务器输出数据的编码

```

2. 会话变量

```
会话变量： 只存在于当前客户端与数据库服务器端的一次连接当中。如果连接断开，那么会话变量全部丢失！
-- 定义会话变量: set @变量=值
-- 查看会话变量： select @变量
```

3. 局部变量
> 局部变量： 在存储过程中使用的变量就叫局部变量。只要存储过程执行完毕，局部变量就丢失！！

## 带有输入输出参数的存储过程
```
```mysql
DELIMITER $

CREATE PROCEDURE pro_testInOut(INOUT n INT)

BEGIN

	SELECT n;

	SET n=50;

END $

//使用

SET @n=10;

CALL pro_testInOut(@n);	-- n 10

SELECT @n;	-- n 50

```




## 带有条件判断的存储过程
1. If -- then -- elseif -- then --else  --end if
2. this ,end if 后面有分号;


```
-- 需求：输入一个整数，
	如果1，则返回“星期一”,
	如果2，返回“星期二”,
	如果3，返回“星期三”。
	其他数字，返回“错误输入”;

DELIMITER $ 
CREATE PROCEDURE pro_testIf(IN n INT,OUT str VARCHAR(20))
BEGIN
	IF n=1 THEN 
		SET str = '星期一';
	ELSEIF n=2 THEN
	SET str='星期二';
	ELSEIF n=3 THEN
	SET str = '星期四';
	ELSE 
	SET str='输入错误';
	END IF;
END $

//使用
CALL  pro_testIf(1,@str)
SELECT @str	-- str 星期一
```

## 带有循环功能的存储过程
1. 需要声明会话变量
2. while -- do -- end while;
3. do 后面的都要分号
4. end while 要分号


```
-- 需求： 输入一个整数，求和。例如，输入100，统计1-100的和
BEGIN
	DECLARE i  INT DEFAULT 1;
	DECLARE vsum INT default 0;
	WHILE i<=num DO
	SET vsum = vsum+i;
	SET i=i+1;
	END WHILE;
	SET result = vsum;
END $

//使用
CALL  pro_testWhile(100,@result);
SELECT @result;		-- 5050
```

## 使用查询的结果赋值给变量 into
```
DELIMITER $ 
CREATE PROCEDURE pro_finById2(IN eid INT,OUT vname VARCHAR(20))
BEGIN
	SELECT empName INTO vname FROM employee  WHERE id =eid;
END $

//使用

CALL pro_finById2(1,@name)
SELECT @name
```

## 删除存储过程
DROP PROCEDURE pro_testOut;


# 触发器
> 当操作了某张表时,希望同时触发一些动作/行为 可以使用触发器
> 当向员工表插入一条记录时，希望同时往日志表插入数据

动作:
1. 添加
2. 修改
3. 删除

## 修改
```
需求： 当向员工表插入一条记录时，希望mysql自动同时往日志表插入数据
-- 创建触发器(添加)

CREATE TABLE test_log(
	id INT(4) ZEROFILL PRIMARY KEY AUTO_INCREMENT,
	content VARCHAR(20)
)

CREATE TRIGGER tri_update AFTER UPDATE ON employee FOR EACH ROW
	INSERT INTO test_log(content) VALUES ('员工表修改了一条记录')
	

UPDATE employee SET empName ='123' WHERE id =5

SELECT * FROM test_log;		0001 员工表修改了一条记录

//测试
CREATE TRIGGER tri_empUpd AFTER UPDATE ON employee FOR EACH ROW    -- 当往员工表修改一条记录时
     INSERT INTO test_log(content) VALUES('员工表修改了一条记录');

```
## 添加

```
CREATE TRIGGER tri_empAdd AFTER INSERT ON employee FOR EACH ROW    -- 当往员工表插入一条记录时
     INSERT INTO test_log(content) VALUES('员工表插入了一条记录');

INSERT INTO employee(id,empName,deptId) VALUES(5,'扎古斯',1);
```
## 删除

```
CREATE TRIGGER tri_empDel AFTER DELETE ON employee FOR EACH ROW    -- 当往员工表删除一条记录时
     INSERT INTO test_log(content) VALUES('员工表删除了一条记录');

```


# mysql权限问题

1. root :拥有所有权限(可以干任何事情);
2. 权限账户: 只拥有部分权限(CRUD) :例如:只能操作某个数据库的某张表
3. 修改mysql 的密码
   1. 数据库的密码采用MD5加密(单向加密)

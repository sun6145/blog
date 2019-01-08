---
title: javaeeday04
tags:
  - null
  - null
date: 2018-08-28 09:05:04
categories:
top:
---




# 视图

> 数据库对象,它是一个虚拟表,底层并非以键值形式保存数据,在执行过程中动态从基表中获取数据(视图以表为底层来查询)

1. 增删改视图的时候,表的数据也会修改
2. 视图实际上表的上层结构,表的映射.


<!-- more -->

## 视图的基本语法

### 创建

```sql
# 创建视图
create view view_name
as
select * from emp where deptno =10

# 执行增删改查操作和表一致
select * from view_name;
update view_name set sal =2500 where empno = 7782;
```

1. with check option:

   > 保护视图数据,只能在视图范围之内进行数据的修改
   >
   > 修改视图会修改底层基表的数据
   >
   > 视图一般只执行查询,不执行增删改

```sql
# 只能在视图的范围内修改
create or replace view view_name
as
select * from emp where deptno =10 with check option;

# 成功
update view_name set sal =2500 where empno = 7782;

# 失败
update view_name set deptno=20 #1369 - CHECK OPTION failed 'bd1807.view_name'

```

### 销毁

> drop  view view_name;

## 视图的优点

1. 安全性:保密敏感数据,(视图中不给出敏感信息)
2. 高效性:提高查询效率
3. 定制化数据: 可以将多张表中经常展示数据放置在视图中快速查询(可以多张表联合查询放入视图)



# 索引

> 提升查询效率而创建数据结构
>
> 不填加索引,全表扫描
>
> INNODB本身存储的就是以树的形式.
>
> 树的深度越浅,检索越好,尽量平衡

## 索引的分类

1. **B-Tress索引:**
   1. mysql使用B+Tree:方法来卫华索引列数据
   2. B+树是一个多路搜索树

   > 不同的存储引擎对索引的存储策略不一致

   1. myisam:叶子节点中保存记录的地址
   2. innodb:叶子节点中直接保存相应数据

2. 位图索引

3. 哈希索引

## 创建

1. **普通索引**

```sql
#根据ename 查询记录
# 不适用索引
explain select * from emp where ename = 'scott'
type: ALL 			//最慢all(全表扫描),最好能达到 reg ,最好 system
rows: 14

# 普通索引
create index index_ename on emp(ename);
type: ref
rows: 1
```

2. 唯一索引

   > create unique index index_name on 表(字段)



3. 全文索引

4. 空间索引
5. 复合索引: 多列作为索引字段 on 表(字段,字段)

## 删除

```sql
drop index 名字 on 表
```

## 索引的选择

> 索引需要单独的文件来保存维护
>
> 表数据发生变化需要维护索引表

适合添加索引

- 表	
  1. 表的数据量足够大(10万条左右)
  2. 增删改操作较少(3%~%5)
- 字段
  1. 高基数列(不一样的数据较多的:高基数)





## 索引的说明

1. 索引不适合添加过多

2. 经常作为查询条件的列适合作为索引

3. 有些特殊情况下,索引会失效

   如: 给enamet添加索引后, ename like '%s%',包含的情况,走的是全表

# 数据库的设计

## 数据库设计的含义

> 查询和项目的(基础)基石,数据库的优化的前提就是数据库的设计.

## 数据库设计步骤

1. 需求分析
2. 概念结构设计:E-R模型图
3. 关系模型: 将ER图转换为表,设置外模式
4. 物理设计: 
5. 调试
6. 运行和维护

## 数据库设计的范式

1. 1NF: 所有域都原子性的,域中的数据不可分割
2. 2NF: 非主键字段必须与组件相关(每张表只描述一列事务),而不能与主键部分相关(联合主键),降低数据的冗余
3. 3NF:所有的非主键必须与主键直接相关,非主键字段之间不直接相关

### E-R图(实体关系图)

> 客观世界的抽象的模型展示

1. 实体: 矩形框

2. 关系: 菱形框

3. 属性: 椭圆形框

4. 连线: 


### 实体之间的关系

- 1对1: 个人信息 ---- 档案
  - 外键添加唯一约束
  - 主键作外键
- 1对多(多对1):
  - 在多的一方添加外键
- 多对多:
  - 添加第三张关系表
  - 设置联合主键和外键
  - 将多对多的关系独立出一张表
  - 表里面联合主键和二个表的主键作为外键





# 数据库的优化

sql 优化:

1. 尽量减少* 的使用(* 转换为列名, 然后在查询)

2. 索引失效的情况

   1. 索引列不要使用 is not null /is null
   2. 索引列上不要使用函数
   3. 索引列上不要计算
   4. 索引列不要使用not(!= / <>)
   5. 索引列不要使用or(union 替换)
   6. 索引列不要使用包含(like '%s%')

3. 用exists 替代 in ,用not exists 代替not in

   二者的选择:

   - exists 先执行主查询
   - in先执行的子查询
   - 先过滤后关联

# 备份和还原

1. 备份

   mysqldump -uroot -proot dbname > d:/back.sql

2. 还原

   mysql -u root -p < c:/bac.sql

> mysql.exe  : 安装
>
> mysqld.exe: 安装和卸载mysql服务
>
> 	mysqld  -remove mysql
>	
> 	mysqld -installed





# JDBC

> 数据库连接的技术(Java Data Base Connectivity)

- jdbc 由一套接口组成,提供了关系型数据库统一访问

- 驱动则是数据库厂商对jdbc接口的具体实现,开发人员只需要学会jdbc接口的调用,导入相应驱动实现就可以实现对不同数据库的操作

实现jdbc开发: jdbc接口 + 数据库的驱动

jdbc driver manage: 对底层不同数据库的调用,对上提供统一的管理

## jdbc开发步骤

1. Driver Manage:注册和管理驱动

2. 建立连接(java app ----> mysql  serverTCP)

3. 发送sql命令给数据库服务器
4. 接收数据库响应处理结果集
5. 释放资源(连接资源)
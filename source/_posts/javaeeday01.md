---
title: javaeeday01
tags:
  - null
  - null
date: 2018-08-23 09:06:32
categories:
top:
---

mysql的使用

<!-- more -->

# 引入

1. 内存中的数据: 速度快,不能持久性保存
2. 文件中的数据,随着文件的增大,检索困难
3. 数据库产生60年

# 数据

> 是客观事物的符号表示
>
> 符号:模拟,数字

## 数据量庞大

1. 保存介质
   - 纸张,u盘,硬盘,网盘
2. 检索难度增加
   1. 文件上层对组织来进行封装(文档型数据库)





## 数据库(Database)

> 按照一定数据结构存储数据的仓库



### 数据库管理系统(DBMS)

>  概念: 操纵和管理数据库的软件

用户向DBMS发起请求,DBMS调用底层,得到结果后返回数据给用户

### 数据库分类

### 关系型数据库管理系统(RDBMS)

> 采用**关系模型**管理数据 ,采用**二维表格**的形式来简化数据关系实现对数据的管理

​	常见关系型数据库:

​		1. oracle数据库:oracle公司产品,大型分布式数据库管理系统.大型企业,金融,国企,产品免费,服务收费

  2. mysql数据库:oracle公司,针对中小型系统;开源 免费(闭源风险).互联网厂商
3. mariaDB 数据库:mysql分支,社区维护5.5版本之前和mysql无区别.
4. Sql Server :Microsoft 中小型企业.只能与windsows运行
5. DB2:IBM公司,中小型企业,外企,银行

### 非关系型的数据库管理系统(Nosql)

1. not only sql
2. 弥补关系型数据库在高io,高可用方面的局限性

非关系型数据库

1. redis
2. hbase
3. mongodb
4. neo4j(图数据库)

# mysql

## mysql初识

1. mysql是一个关系型的数据库管理系统
2. 采用关系模型对数据进行管理,对不同的**数据采用分****表**的形式管理,多张表存在的情况下,可以采用分库的形式进行管理
3. 数据库可以存放多张表,mysql数据库DBMS可以管理多个数据库

> mysql数据库管理系统中可以管理多个数据库,在一个数据库中可以存放多张表

4. mysql体积小,开源免费,支持GPL(开源软许可证)协议
5. 1996年发布mysql1.0,mysql ab公司
6. 2000年 正式开源
7. 2008年被sun以10亿美元收购
8. 2009年sun被oracle以74亿美元收购

## mysql Server端的下载和安装

> 提供数据库服务:响应请求

1. 下载: [mysql下载](https://dev.mysql.com/downloads/mysql/)
2. 安装
   1. 选择produces
   2. 配置
      1. Type and NotWorking 
         1. standalone(单节点)
      2. Type and Networking
         1. Development Machine
         2. TCP/IP port,open FireWall
      3. Accounts and Roles
         1. accounts: 特定用户才能连接数据库服务
         2. roles: 角色,控制权限,权限集合
         3. 用户: root用户
         4. 其他用户:
      4. Windows Service
         1. Service Name:MySQL
         2. start the mysql at System Satartup:开机自启
      5. Advanced Options:日志管理
         1. ErrorLog:mysql_err.err

### 文件存放目录

Data dir : 存放数据文件

- Data :每创建一个数据库就会创建相应的文件夹
  - frm 保存表的结构信息
- my.ini: mysql的配置文件

   - 跳过授权验证

     `skip-grant-table`

# mysql操作



### 客户端

> 发请求给客户端

1. WorkBench就是一个客户端;自带图形化界面(mysql安装时可选)
2. 命令行客户端

### 命令行客户端操作

1. 登录
   - `mysql -u root -proot 回车`
   - `mysql -u root -p回车`
2. 展示所有的数据库: `show databases;`
3. 选择数据库:` use databaseName`;
4. 展示数据库中所有的表`show databases;`
5. net satrt mysql
6. net stop mysql
7. mysqld --install 服务名

TCP/IP:三次握手:

1. 客户端向服务器发送

### 图形化界面

1. 

2. 

3. 

   

### 表中的术语

1. 记录:行
2. 字段:列

# SQL

> structure query language:结构化查询语言
>
> 在关系型数据库中进行操作标准语言,编程语言

1. 对大小写不敏感,
2. 以分号(;)j结尾

## sql 分类

### DDL

>  数据定义语言,数据库对象的操作(结构)

1. create
2. alter
3. drop

### DML

> 数据操纵语言(数据操作: 增删改查)

1. insert 

2. update

3. delete

4. select

   

### DCL

> 数据的控制语言(授权 : 取消权限)

1. grant : 授权
2. revoke: 取消授权

### TCL

> 事务控制语言

1. commmit : 提交
2. rollback: 回滚

## DDL 语言

### 数据库

```sql
# 创建数据库
CREATE DATABASE if not EXISTS bd1807;

# 销毁数据库
drop database if exists bd1807;
```

### 表

1. 创建

   ```sql
   # 创建表
   create table userinfo(
   	uid int,
   	uname varchar(20),
   	gender bit(1),
   	birthday date,
   	tel VARCHAR(11)
   	
   );
   ```

2. 查看表的结构

   ```sql
   desc userinfo;
   
   图形化工具:右键 --> 设计表
   ```

3. 表结构的修改

   1. 添加字段:add(追加)

      ```sql
      # 追加添加
      ALTER table userinfo add QQ varchar(20);
      
      # 添加字段到首位
      alter table userinfo add wechat varchar(20) first;	-- first 添加到首位
      
      # 添加到uid之后
      alter table userinfo add renren varchar(20) after uid;	-- after 添加字段之后
      ```

      

   2. change/modify

      1. change : 旧字段名称 新名称 类型 约束;
      2. modify :字段 类型 约束(不能修改名称) 

   3. 修改字段名称:

      ```sql
      # 修改字段名称 QQ--> t_qq
      alter table userinfo change QQ t_qq VARCHAR(20);
      ```

      

   4. 类型修改

      ```sql
      # 修改varchar() 
      alter table userinfo change t_qq t_qq varchar(10);
      alter table userinfo MODIFY t_qq VARCHAR(20);
      ```

      

   5. 位置

      ```sql
      # 修改位置
      alter table userinfo MODIFY t_qq varchar(20) after birthday;
      ```

      

   6. 销毁字段:drop

      ```sql
      # 删除renren字段
      alter table userinfo drop renren;
      
      ```

   7. 表名称的修改:rename to

      ```
      # 修改表的名称
      rename table userinfo to `user`;#``引号区分关键字
      ```

   8. 清空表(清除数据,不清楚结构)

      ```sql
      # 清空表的数据
      truncate table userinfo;
      ```

      

### mysql数据类型

> 在不同的数据库之间,数据类型是有区别的,oracle中数值表示为number
>
> 性别: bit(1)



|            |                             |                                |
| ---------- | --------------------------: | -----------------------------: |
| int        |              10位(21亿左右) |                                |
|            |                             |                                |
| float(m,n) |          默认(10,2)精度24位 |      需要声明总长度,小数点位数 |
| double     |             (16,4),精度53位 |                 有可能损失精度 |
| deccimal   | 没有默认,必须声明长度和精度 |               用于金融数据保存 |
|            |                             |                                |
| char(n)    |              定长长度字符串 |          不足空格填补,多出报错 |
| varchar(n) |              可变长度字符串 |                                |
| text       |                      大文本 |                  最大65535字符 |
|            |                             |                                |
| date       |                        日期 |                     YYYY-MM-DD |
| time       |                        时间 |                       HH:mm:SS |
| datetime   |                    日期时间 |            YYYY-MM-DD HH:MM:SS |
|            |         1000-01-01 00:00:00 |            9999-12-31 23:59:59 |
| timestamp  |                      时间戳 |                 YYYYMMSSHHMMSS |
|            |                     1970-01 |                        2037-12 |
| year       |                          年 |                                |
|            |                             |                                |
| blob       |              存放二进制数据 | 存储图片(一般只存储文件的位置) |
| enum       |                        枚举 |                                |
| set        |                    集合类型 |                                |
| json       |                    json数据 |                                |
|            |                             |                                |


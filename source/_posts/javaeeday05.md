---
title: javaeeday05
tags:
  - null
  - null
date: 2018-08-29 09:03:25
categories:
top:
---



# jdbc开发

## 开始

1. 新建java工程
2. 新建lib文件夹,用来放library文件;
3. 导入jdbc的实现类(数据库的jar)
4. 右键jar---> buildPath ----> add to build path

<!-- more -->

## 连接数据库

1. 注册驱动,Drivermanager管理具体的驱动程序,实现对底层屏蔽,对开发人员提供统一的访问

2. 建立连接

   1. url :    jdbc:公共协议+子协议+ip地址+数据库名字
   2. user:  用户名
   3. password:   密码


## 发送sql

>  Statement对象:实现sql发送	`Statement statement = connection.createStatement();`

- statement.executeQuery(sql);//查询语句,返回一个结果集
- statement.executeUpdate(sql); //增删改,DDL,返回影响的行数##
- statement.executeBatch();//批处理,返回int[]

## 处理响应结果

> ResultSet底层维护了一个指向结果集的游标
>
> 程序中每次读取一行,一个一个读(游标指向行的(字段)信息)

- next():游标向下,返回boolean类型,true有数据
- getInt(String str):通过字段名称获取

```java
ResultSet result = statement.executeQuery(sql);// 查询语句,返回一个结果集

```

## 释放资源

```java
finally {
    try {
        if(connection!=null) {
            connection.close();
        }
        if(statement!=null) {
            statement.close();
        }
        if(result!=null) {
            result.close();
        }
    } catch (SQLException e) {
        // TODO Auto-generated catch block
        e.printStackTrace();
    }
}
```

# 常见API详解

## DriverManager

> (java.sql.DriverManager)

- 注册驱动只有registerDriver();

1. 提供驱动的基础服务,对驱动实现细节进行屏蔽
2. 注册加载驱动,用于获取连接

```sql
public class Driver extends NonRegisteringDriver implements java.sql.Driver {
    //
    // Register ourselves with the DriverManager
    //
    static {
        try {
            java.sql.DriverManager.registerDriver(new Driver());
        } catch (SQLException E) {
            throw new RuntimeException("Can't register driver!");
        }
    }
}
```

### 加载驱动的方式

> 推荐使用类加载,只注册一次

1. 创建Driver对象,注册了二次驱动

   ```java
   import com.mysql.jdbc.Driver;
   
   DriverManager.registerDriver(new Driver());
   ```

2. 类加载,使用静态代码块

   ```java
   Class.forName("com.mysql.jdbc.Driver");// 静态代码块
   ```


## Connection

> 1. 代表客户端程序与数据库的连接
> 2. 与数据的交互都需要依赖于此连接

### 方法:

1. createStatement():
2. prepareStatement(sql);
3. setAutoCommit(false);
4. commit();
5. rollback();
6. getMetaData();获取连接的元数据信息,和连接相关的数据(元数据:解释数据的数据)
   1. getDatabaseProductName():数据库产品的名字
   2. getDriverName():获取驱动的信息
   3. getURL(): 获取连接的URL;

```java
// jdbc:公共协议+子协议+ip地址+数据库名字
// ? 参数
// useSSL=true SSL(Secure Sockets Layer 安全套接层),
// 及其继任者传输层安全(Transport Layer Security,TLS)是为网络通信提供安全及数据完整性的一种安全协议
Connection connection = null;
connection = DriverManager.getConnection(
    "jdbc:mysql://localhost:3306/bd1807?useSSL=true", 
    "root",
    "123456"
);

DatabaseMetaData metaData = connection.getMetaData();
metaData.getDatabaseProductName();//数据库产品的名字
System.out.println(metaData.getDriverName());//获取驱动的
metaData.getURL();
// 能得到connection,说明数据库已经连接
System.out.println(connection);
```

```java
DatabaseMetaData metaData = connection.getMetaData();
metaData.getDatabaseProductName();//数据库产品的名字
System.out.println(metaData.getDriverName());//获取驱动的信息
metaData.getURL();
```



## statement

1. executeQuery(sql);	// 查询语句,返回一个结果集
2. executeUpdate(sql);   //增删改,DDL,返回影响的行数
3. executeBatch();       //批处理,返回int[]

## ResultSet

> 1. 代表返回结果的结果集
>
> 2. 底层维护游标
>
> 3. next():移动游标获取每一行数据
>
> 4. getMetaData(): 获取结果集的元数据
>
>    1. getColumnCount():获取结果的列
>
>    2. getColumnName(int column):获取对应列的名字
>
>       > 遍历的时候,得到字段的名字,通过反射,获得属性Field,赋值封装到对象

### 方法

1. boolean next(): 向前移动游标(遍历一行)
2. getInt("字段名");
3. getString("字段名")







# 分层开发

1. 实现解耦

2. dao层(data access obeject)

    	1. 接口
    	2. 实现类

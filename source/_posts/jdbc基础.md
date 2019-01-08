---
title: jdbc基础
tags:
  - null
  - null
date: 2018-08-20 22:56:03
categories:
top:
---

# JDBC简介

数据库的驱动

SUN公司为了**简化、统一对数据库的操作**，定义了一套Java操作数据库的规范，称之为JDBC。

JDBC全称为：**Java Data Base Connectivity**（java数据库连接），它主要由接口组成。

组成JDBC的２个包：　`java.sql　`,`javax.sql`

以上2个包已经包含在J2SE中，所以不用导入，

开发这只需要导入JDBC的实现类即数据库驱动包。开发JDBC应用需要以上2个包的支持外，还需要导入相应JDBC的数据库实现(即数据库驱动)。

## JDBC可以操作数据库吗？



<!-- more -->

不能直接操作，需要加载驱动程序

![JDBC](https://raw.githubusercontent.com/sun6145/githubPicture/master/img/jdbc.png)



## JDBC之前的使用步骤

	1. 通过mysql的客户端工具，登录数据库服务器  （mysql -u root -p 密码）
	2. 编写sql语句
	3. 发送sql语句到数据库服务器执行

# 使用

## 前提

1. 登录数据库服务器,url:` jdbc协议:数据库子协议:主机:端口/连接的数据库   `
   1. 数据库的IP地址
   2. 端口
   3. 数据库用户名
   4. 密码

## 第一个JDBC程序



## **JDBC接口核心的API**

> java.sql.*   和  javax.sql.*

### Driver接口

> 表示java驱动程序接口。所有的具体的数据库厂商要来实现此接口。

1. connect(url,properties):连接数据库的方法
   1. url: 连接数据库的URL 
   2. URL语法： jdbc协议:数据库子协议://主机:端口/数据库
      1. user： 数据库的用户名
      2. password： 数据库用户密码

```java
//使用之前需要导入jdbc的实现驱动包.
String url = "jdbc:mysql//lcoalhost:3307/day01";
private String user = "root";
private String password = "123456";
public void test1() {
    // 1.创建驱动程序类对象
    Driver driver = null;
    try {
        driver = new com.mysql.jdbc.Driver();
    } catch (SQLException e1) {
        // TODO Auto-generated catch block
        e1.printStackTrace();
    }

    // 2.连接数据库,得到的Connection
    //"user","password"
    Properties properties = new Properties();
    properties.setProperty("user", user);
    properties.setProperty("password", password);
    try {
        Connection connect = driver.connect(url, properties);
    } catch (SQLException e) {
        // TODO Auto-generated catch block
        e.printStackTrace();
    }
}
```



### DriverManager类

> 驱动管理器类，用于管理所有注册的驱动程序

1. registerDriver(driver)  : 注册驱动类对象
   1. new driver的时候,就注册了自身,所以注册了二次,不没必要
2. Connection getConnection(url,user,password);  获取连接对象

 

### Connection接口

> 表示java程序和数据库的连接对象。

1. Statement createStatement() ： 创建Statement对象
2. PreparedStatement prepareStatement(String sql)  创建PreparedStatement对象
3. CallableStatement prepareCall(String sql) 创建CallableStatement对象
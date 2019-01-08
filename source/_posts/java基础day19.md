---
title: java基础day19
tags:
  - null
  - null
date: 2018-08-22 09:20:13
categories:
top:
---

# 自定义注解

```java
@interface 注解名{
	类型 成员名()default 默认值;
    .....
}
```

<!-- more -->

## java 元注解:

> 　元注解的作用就是负责注解其他注解。 

1. @Target, 　　　　
2. @Retention, 　　　　
3. @Documented, 　　　　
4. @Inherited 

### @Retention

> 用于声明我们自定义的注解的存活时间，

它的属性value有三个对应的值  

1.  RetentionPolicy.SOURCE：在编译阶段丢弃。这些注解在编译结束之后就不再有任何意义，所以它们不会写入字节码。@Override, @SuppressWarnings都属于这类注解。  
2. RetentionPolicy.CLASS：默认值，编译器仅把注解保存在class文件中，在运行java程序时，JVM不会保留注释，即不能用反射（在运行期）来获取注释  
3. RetentionPolicy.RUNTIME：编译器不仅把注解保存在class文件中，同时在运行java程序时，JVM也会保留注释，即可以通过反射来获取注释 

###  @Target

> 用于指定自定义的注解能够修饰哪些元素，

其value属性指明了注解对象，有如下几个值供选择： 

- ElementType.TYPE:修饰类、接口或枚举（enum) 
- ElementType.FIELD:注解成员变量 
- ElementType.METHOD：注解方法 
- ElementType.PARAMETER：注解方法参数 
- ElementType.CONSTRUCTOR：注解构造函数 
- ElementType.LOCAL_VARIABLE ：注解局部变量 
- ElementType.ANNOTATION_TYPE 注解另一个自定义注解 
- ElementType.PACKAGE 注解包

# 3. @Documented

# 网络编程

使用的包位于`java.net.*`

## 协议

> 网络中计算机之间通信的规则

常用的协议:

J常用的协议巨

1. IP:网际协议
2. TCP:传输控制协议
3. 超文本传输协议(HTTP)
4. 文件传输协议(FTP)
5. 简单邮件传输协议(SMTP)

## IP地址

表达方式

1. 二进制(32位)
2. 点分十进制

### IP地址的分类

> 网络号+主机号

1. A类:0+7位主机号+24主机号
   - `0.0.0.0~127.255.255.255`(0和127不可用)
2. B类
   - `128.0.0.0~191.255.255.255`
3. C类

## InetAddress类



### 常用方法

1. getLocalHost():获得IP地址
2. get

## DNS

> 域名解析

## 端口

> 用于实现程序间的通信

### 常用的公认端口

|       协议       | 端口 |
| :--------------: | :--- |
| 简单邮件传输协议 | 25   |
|   文本传输协议   | 21   |
|  超文本传输协议  | 80   |

## URL

> 统一资源定位器
>
> 标识网络上的摸一个资源

格式:

`protocol://hostmame[:port]/path/[?query]#fragment`

`协议://主机地址:端口号/目录/查询字符串#片段`

### 常用方法

1. getProtocol( )
2. getHost( )
3. getPort()
4. getDefaultPort()
5. getPath()
6. getQuery()
7. getRef( )

## 网络通信

> java支持TCP和UDP协议族

### TCP/IP

1. 较可靠的双向流协议
2. 发送任意数量的数据

### UDP

1. 是一种不可靠的连接
2. 速度快

### 通信的模式

1. 客户端/服务器模式(C/S)
2. 浏览器(B/S)

## Socket客户端

> 提供了二个双向流,可以互相通信

## ServerSocket


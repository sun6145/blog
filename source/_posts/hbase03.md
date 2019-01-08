---
title: hbase03
tags:
  - null
  - null
date: 2018-11-02 09:30:06
categories:
top:
---

<!-- more -->

#  mapreduce如何读取habase

MapReduce 程序读取到hbase的数据的时候 ,其实一个result

map犯法每次执行一次接收的参数就是一个record



## 存储hbase

shell : `put "user_info" ,"rk01","base_info:name","huangbo"`

API:`	table.put(new Put(rowkey,cf,qualifier,value))`

hbase在进行插入的粒度,其实是cell级别

​	每次插入的就是一个Mutation

​		put,delete

​	查询出来的就是一个cell

# HBase  结合 MapReduce


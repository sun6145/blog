---
title: sqoop
tags:
  - null
  - null
date: 2018-10-30 21:19:29
categories:
top:
---

# sqoop：
概念及功能：
​	数据迁移的，数据移动
​	原来数据管理是集中式管理
​	将原来的集中式管理的数据  移动到分布式平台下
​	sqoop就是将传统的关系型数据库的数据和大数据据平台之间的数据迁移
​	迁移有两个方向：
​		1）关系型数据库mysql/oracle----》大数据平台（hadoop,hive,hbase）
​			数据导入
​		2）大数据平台（hadoop  hive  hbase）----》关系型数据库(mysql)
​			数据导出
​			通常用于  分析结果数据从hive或hdfs导出到mysql中  便于前台展示
​		sqoop是关系型数据库和大数据平台的桥梁	
​		

		注意：这里的大数据平台指的是hadoop的广义概念
			hadoop：
				1）狭义
					专门指hadoop组件
				2）广义：
					hadoop生态圈的所有组件
					hadoop  hive   hbase   zookeeper ......<!-- more -->

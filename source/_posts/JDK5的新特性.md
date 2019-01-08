---
title: JDK5的新特性
tags:
  - JDK5特性
date: 2018-08-03 10:32:19
categories: Java
top:
---
# 新特性
自动拆装箱,泛型,增强for,静态导入,可变参数,枚举

<!-- more -->

# 增强for

## 增强for概述
> 简化数组和Collection集合的遍历
> 格式：
	
	for(元素数据类型 变量 : 数组或者Collection集合) {
		使用变量即可，该变量就是元素
    }

注意事项
	- 增强for的对象要判断是否为null.

```
List<String> list = null;
// NullPointerException
// 这个s是我们从list里面获取出来的，在获取前，最好做一个判断
// 说白了，这就是迭代器的功能
if (list != null) {
	for (String s : list) {
		System.out.println(s);
	}
}

// 增强for其实是用来替代迭代器的
// ConcurrentModificationException,迭代器也不能并发修改
for (String s : array) {
	if ("world".equals(s)) {
		array.add("javaee");
	}
}
```

# 静态导入
# 格式
	- import static 包名... .类名... .方法名;	
	- 可以直接导入到方法的级别	

```
System.out.println(java.lang.Math.abs(-100));
System.out.println(java.lang.Math.pow(2, 3));
System.out.println(java.lang.Math.max(20, 30));
写法太复杂,出现导包.
=============================
import  java.lang.Math;

System.out.println(Math.abs(-100));
System.out.println(Math.pow(2, 3));
System.out.println(Math.max(20, 30));
还是觉得复杂,出现静态导入.
=============================
import static java.lang.Math.abs;
import static java.lang.Math.pow;
import static java.lang.Math.max;

System.out.println(abs(-100));
System.out.println(pow(2, 3));
System.out.println(max(20, 30));
```

## 注意事项
方法必须是静态的
如果有多个同名的静态方法，容易不知道使用谁?这个时候要使用，必须加前缀。由此可见，意义不大，所以一般不用，但是要能看懂。
```
import static java.lang.Math.abs;
import static java.lang.Math.pow;
import static java.lang.Math.max;

public static void abs(String s){
	System.out.println(s);
}

// System.out.println(abs(-100));	//没有前缀,编译期间报错
System.out.println(java.lang.Math.abs(-100));	//需要加前缀
System.out.println(pow(2, 3));
System.out.println(max(20, 30));
```

# 可变参数
> 定义方法的时候不知道该定义多少个参数

格式：`修饰符 返回值类型 方法名(数据类型…  变量名){}`

> 这里的变量其实是一个数组
如果一个方法有可变参数，并且有多个参数，那么，可变参数肯定是最后一个
```
//多个数求和
public static int sum(int... a) {
	int s = 0;
	for(int x : a){
		s +=x;
	}
	return s;
}

测试:
int result = sum(10,20);	//编译器:result=sum(new int[] {a, b});
result = sum(10,20,30);
result = sum(10,20,30,40);
result = sum(10,20,30,40,50);

==================================
public static int sum(int b ,int... a) {
	int s = 0;
	for(int x : a){
		s +=x;
	}
	return s;
}

// 程序会把第一个数据赋值给b,剩下的给数组a,

public static int sum(int... a,int b )	//编译期间报错,全部给了a,b没有赋值
```


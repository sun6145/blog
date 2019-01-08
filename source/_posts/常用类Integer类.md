---
title: Integer类
date: 2018-7-29 9:23:5
categories: Java
tags: 
	- 包装类
	- Integer
top:  # 置顶（0-100）
---
# 包装类
> 为了对基本书类型进行更多的操作，Java针对每一种数据类型提供对应的类类型---包装类类型

<!-- more -->

## Integer

### Integer查看数据类型的范围
- 最大值：`public static final int MAX_VALUE;`
- 最小值：`public static final int MIN_VALUE;`

### Integer构造函数
* public Integer(int value)
* public Integer(String s)
    注意：这个字符串必须是由数字字符组成
	
```
// 方式1
int i = 100;
Integer ii = new Integer(i);
System.out.println("ii:" + ii); //ii:100

// 方式2
String s = "100";
// NumberFormatException
// String s = "abc";
Integer iii = new Integer(s);
System.out.println("iii:" + iii);   //iii:100
```

### int类型和String类型的相互转换

- int ---> String 
    1. `"" + int number`
    2. `String.valueof(int number)`
    3. `int ---> Integer -----> String`
        ```
        Integer i = new Integer(int number);
        String str1 = i.toString();
        ```
    4. `public static String toString();`
        ```
        Integer.toString(int number)
        ```
        
        
- String ----> int
    1. `String ---> Integer --->int`
        ```
        Integer ii = new Integer(str);
        int x = ii.intValue();
        ```
    2. `public static int parseInt(String s)`
        - `Integer.parseInt(String s);`

### <a href="#shift">Integer进制转换</a>

- 10进制转为2进制
    - public static String toBinaryString(int i);
- 10进制转8进制
    - public static String toOctalString(int i);
- 10进制转16进制
    - public static String toHexString(int i);
- 十进制到其他进制(范围：2-36，因为表示的数有0-9,a-z共36个)
    - public static String toString(int i,int radix);
- 其他进制到十进制
    - public static int parseInt(String s,int radix);
    
```
System.out.println(Integer.parseInt("100", 10));    //100
System.out.println(Integer.parseInt("100", 2));     //4
System.out.println(Integer.parseInt("100", 8));     //64
System.out.println(Integer.parseInt("100", 16));    //256
System.out.println(Integer.parseInt("100", 23));    //529
```

## JDK5的新特性
- 自动装箱： 把基本类型转换为包装类类型
- 自动拆箱： 把包装类类型转换为基本类型

```
//定义一个int类型的包装类类型变量ii
Integer ii = new Integer(100); //  Integer ii=100;
ii+=200;

//实际上面代码：
Integer ii = new Integer(100);
ii=Integer.valueof(ii.intValue() + 200);    //自动拆箱后，再自动装箱

使用注意的问题：
	Integer i = null;
		代码会报NullPointExpection
		Integer iii = null;
	// NullPointerException
	if (iii != null) {
		iii += 1000;
		System.out.println(iii);
	}
```
### 缓冲池
> **Integer的数据直接赋值，如果在-128到127之间，会直接从缓冲池里获取数据**

## 练习
```
        Integer i1 = new Integer(127);
		Integer i2 = new Integer(127);	
		System.out.println(i1 == i2);			//false
		System.out.println(i1.equals(i2));		//true
		System.out.println("-----------");

		Integer i3 = new Integer(128);
		Integer i4 = new Integer(128);
		System.out.println(i3 == i4);			//false
		System.out.println(i3.equals(i4));		//true
		System.out.println("-----------");

		Integer i5 = 128;
		Integer i6 = 128;
		System.out.println(i5 == i6);			//false  <--  数据缓存池范围：-128 - 127
		System.out.println(i5.equals(i6));		//true
		System.out.println("-----------");

		Integer i7 = 127;
		Integer i8 = 127;
		System.out.println(i7 == i8);			//true
		System.out.println(i7.equals(i8));		//true
```

## 总结

### Integer 类的成员方法

- public int intValue()　　//把Integer类型转化为Int类型
- public static int parseInt(String s)　　//把String类型转化为Int类型
- public static int parseInt(String s ,int radix)　　//把String类型转化为radix进制
- public static String toString(int i)　　//把Int类型转化为String类型
- public static Integer toString(int i ,int radix)　　10进制转为radix进制
- public static Integer valueOf(String s)　　　//把String参数给的值，转化为Integer类型
 
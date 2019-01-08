---
title: Date FateFormat类
date: 2018-07-30 17:02:26 
categories: Java
tags: 
	- Date 
	- FateFormat
top:  # 置顶（0-100）
---
# Date 类概述

> Date:表示特定的瞬间，精确到毫秒。 

<!-- more -->

## 构造方法
构造方法：
* Date():根据当前的默认毫秒值创建日期对象
* Date(long date)：根据给定的毫秒值创建日期对象
```
	// 创建对象
	Date d = new Date();
	System.out.println("d:" + d);	//d:Mon Jul 30 16:21:01 CST 2018

	// 创建对象
	// long time = System.currentTimeMillis();
	long time = 1000 * 60 * 60; // 1小时
	Date d2 = new Date(time);
	System.out.println("d2:" + d2);	//d2:Thu Jan 01 08:00:00 CST 1970
```
> 日期类的时间从为什么是从1970年1月1日   
> 最初计算机操作系统是32位，而时间也是用32位表示。
System.out.println(Integer.MAX_VALUE);
2147483647
Integer在JAVA内用32位表 示，因此32位能表示的最大值是2147483647。 
另外1年365天的总秒数是31536000，
2147483647/31536000 = 68.1
> 也就是说32位能表示的最长时间是68年，而实际上到2038年01月19日03时14分07
秒，便会到达最大时间，过了这个时间点，所有32位操作系统时间便会变为
10000000 00000000 00000000 00000000
也就是1901年12月13日20时45分52秒，这样便会出现时间回归的现象，很多软件便会运行异常了。

## 成员方法

 * public long getTime():获取时间，以毫秒为单位
 * public void setTime(long time):设置时间

## 代码测试
```
		// 创建对象
		Date d = new Date();
		// 获取时间
		long time = d.getTime();		
		System.out.println(time);		//1532939861629
		// System.out.println(System.currentTimeMillis());	//1532939861630

		System.out.println("d:" + d);	//d:Mon Jul 30 16:37:41 CST 2018
		// 设置时间
		d.setTime(1000);
		System.out.println("d:" + d);	d:Thu Jan 01 08:00:01 CST 1970

```

# DateFormat 类概述
> DateFormat针对日期进行格式化和针对字符串进行解析的类，因为是抽象类，所以使用其子类SimpleDateFormat

## Date	 --	 String(格式化)
		public final String format(Date date)

## String -- Date(解析)
		public Date parse(String source)

## SimpleDateFormat的构造方法：
 - SimpleDateFormat():默认模式
 * SimpleDateFormat(String pattern):给定的模式构造

```
这个模式字符串该如何写呢?
 * 通过查看API，我们就找到了对应的模式
 * 年 y
 * 月 M	
 * 日 d
 * 时 H
 * 分 m
 * 秒 s
 ```
 
## 格式化 Date -- String
```
// 创建日期对象
Date d = new Date();
// 创建格式化对象
// SimpleDateFormat sdf = new SimpleDateFormat();	//18-7-30 下午5:09(默认格式)
// 给定模式
SimpleDateFormat sdf = new SimpleDateFormat("yyyy年MM月dd日 HH:mm:ss");
// public final String format(Date date)
String s = sdf.format(d);
System.out.println(s);		//2018年07月30日 17:07:17
```

## 解释 String -- Date
```
		String str = "2008-08-08 12:12:12";
		//在把一个字符串解析为日期的时候，请注意格式必须和给定的字符串格式匹配
		SimpleDateFormat sdf2 = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
		Date dd = sdf2.parse(str);
		System.out.println(dd);		//Fri Aug 08 12:12:12 CST 2008
```

## 小案例：算一下你来到这个世界多少天?

```
public static void main(String[] args) throws ParseException {
	// 键盘录入你的出生的年月日
	Scanner sc = new Scanner(System.in);
	System.out.println("请输入你的出生年月日(如：1970-01-01):");
	String line = sc.nextLine();

	// 把该字符串转换为一个日期
	Date date = new SimpleDateFormat("yyyy-MM-dd").parse(line);

	// 通过该日期得到一个毫秒值
	long d = date.getTime();

	// 获取当前时间的毫秒值
	long c = new Date().getTime();

	// 用c-d得到一个毫秒值
	long e = c - d;

	// 把E的毫秒值转换为年
	long day = e / 1000 / 60 / 60 / 24;

	System.out.println("你来到这个世界：" + day + "天");
}
```

# Calendar类:
> 它为特定瞬间与一组诸如 YEAR、MONTH、DAY_OF_MONTH、HOUR 等 日历字段之间的转换提供了一些方法，并为操作日历字段（例如获得下星期的日期）提供了一些方法。

## 成员方法
- public **int** **get**(int field):返回给定日历字段的值。日历类中的每个日历字段都是静态的成员变量，并且是int类型。

```
//获取年
public static void main(String[] args) {
		// 其日历字段已由当前日期和时间初始化：
		Calendar rightNow = Calendar.getInstance(); // 子类对象
		// 获取年
		int year = rightNow.get(Calendar.YEAR);
		// 获取月
		int month = rightNow.get(Calendar.MONTH);
		// 获取日
		int date = rightNow.get(Calendar.DATE);

		System.out.println(year + "年" + (month + 1) + "月" + date + "日");
	}
```
- public **void** **add**(int field,int amount):根据给定的日历字段和对应的时间，来对当前的日历进行操作。
```
public static void main(String[] args) {
		// 获取当前的日历时间
		Calendar c = Calendar.getInstance();

		// 5年后的10天前
		c.add(Calendar.YEAR, 5);
		c.add(Calendar.DATE, -10);

		// 获取年
		year = c.get(Calendar.YEAR);
		// 获取月
		month = c.get(Calendar.MONTH);
		// 获取日
		date = c.get(Calendar.DATE);
		System.out.println(year + "年" + (month + 1) + "月" + date + "日");
```

* public **final** **void** set(int year,int month,int date):设置当前日历的年月日
* 月份从0开始
 
```
Calendar c = Calendar.getInstance();
c.set(1314,05,20);
// 获取年
year = c.get(Calendar.YEAR);
// 获取月
month = c.get(Calendar.MONTH);
// 获取日
date = c.get(Calendar.DATE);
System.out.println(year + "年" + (month + 1) + "月" + date + "日");	//1314年6月20日

```

## 案例
*获取任意一年的二月有多少天*
````
分析：
 * 		A:键盘录入任意的年份
 * 		B:设置日历对象的年月日
 * 			年就是A输入的数据
 * 			月是2
 * 			日是1
 * 		C:把时间往前推一天，就是2月的最后一天
 * 		D:获取这一天输出即可

public static void main(String[] args) {
	// 键盘录入任意的年份
	Scanner sc = new Scanner(System.in);
	System.out.println("请输入年份：");
	int year = sc.nextInt();

	// 设置日历对象的年月日
	Calendar c = Calendar.getInstance();
	c.set(year, 2, 1); // 其实是这一年的3月1日
	// 把时间往前推一天，就是2月的最后一天
	c.add(Calendar.DATE, -1);

	// 获取这一天输出即可
	System.out.println(c.get(Calendar.DATE));
}
```
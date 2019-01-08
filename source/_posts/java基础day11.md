---
title: java基础day11
tags:
  - 包装类
  - 日期
  - 成员内部类
  - 匿名内部类
date: 2018-08-10 09:40:15
categories: JavaSE
top: 
---
# 正则表达式

```
一个典型的调用序列

 Pattern p = Pattern.compile("a*b");
 Matcher m = p.matcher("aaaaab");
 boolean b = m.matches();
```

<!-- more -->

![enter description here](https://www.github.com/sun6145/githubPicture/raw/master/小书匠/正则表达式符号.png "正则表达式符号")

# 自动拆装箱(包装类)
> 包装类是不可变类;对象创建就不能更改
> 包装类使用类常量池技术,提高了性能
> 对包装类的一部分数据进行缓存(除了float和double)

```
类型		缓存数据
byte  	  	-128 ~ 127
char 	 	0 ~ 127
boolean 	全部
```

##  String -- > 数字
new Ingetger(String str).intValue();
Integer.parseInt(String str);
num = Integer.valueof(String);

## 数字 --> String
s = num + "";
s = String.valueof(num);
s = Integer.toString(num);

## 进制转换
```
System.out.println(Integer.toBinaryString(5));	//转换为2进制
System.out.println(Integer.toOctalString(10));	//转换为8进制
System.out.println(Integer.toHexString(15));	//转换为16进制
```
### 十进制变其他进制()
> 除基取余
1. `public static String toString(int i,int radix);`将i变为 radix 进制
	- (范围：2-36，因为表示的数有0-9,a-z共36个)
	```
		System.out.println(Integer.toString(5, 2));//101
	```

### 其他进制 变十进制
> 位 乘以 基的权次幂
1. `public static int parseInt(String s,int radix);` 将字符串s变为 radix 进制
2. `public static int valueOf(String s,int radix);` 将字符串s变为 radix 进制

	```
	System.out.println(Integer.parseInt("101", 2));	//5
	System.out.println(Integer.valueOf("101", 8));	//65
	```

# Date
1. 构造方法:获取当前的时间

	```
	java.util.Date date = new java.util.Date();
	System.out.println(date);	//Sun Aug 12 15:11:48 CST 2018
	//过时的方法,(不建议使用)
	System.out.println(date.getYear() + 1900);
	System.out.println(date.getMonth() + 1);
	System.out.println(date.getDate());
	```
2. public long getTime():获取时间，以毫秒为单位
3. public void setTime(long time):设置时间
## 子类
### Date
> java.sql.Date包下

```
java.sql.Date dt1 = new java.sql.Date(date.getTime());
System.out.println(dt1);		//2018-08-12

//日期 转 字符串 Date.toSring()
String sdt1 = dt1.toString();
System.out.println(sdt1);		/2018-08-12

//字符串 转 日期 Date.valueof(String str)
dt1 = java.sql.Date.valueOf(sdt1);
System.out.println(dt1);		//2018-08-12
```

### Time
```
Time time = new Time(date.getTime());
	System.out.println(time);	//15:20:51
```

## Calendar类
> 月份从0开始
```
Calendar c = Calendar.getInstance();
System.out.println(c.getTime());		//Sun Aug 12 15:23:15 CST 2018
System.out.println(c.get(Calendar.YEAR));	//2018
System.out.println(c.get(Calendar.MONTH)+1);	//8
System.out.println(c.get(Calendar.DATE));		/12

//add()调节时间 
c.add(Calendar.YEAR, -10);		//10年前的今天;		
System.out.println(c.getTime());	//Tue Aug 12 15:24:55 CST 2008
```

## 格式化
```
// 数字的格式化Format
//当前环境 缺省的格式
NumberFormat f1 = NumberFormat.getInstance();
System.out.println(f1.format(234.55665));	//234.557
// 当前环境缺省的货币格式
f1 = NumberFormat.getCurrencyInstance();
System.out.println(f1.format(2.364588));	//￥2.36
```

```
//----------------------日期格式化------------------------------------
		//父类 
		//当前环境缺省格式
		DateFormat  df = DateFormat.getInstance();
		System.out.println(df.format(date));//18-8-10 上午11:47
//==Date=============================================================
		df = DateFormat.getDateInstance();
		System.out.println(df.format(date));//2018-8-10

		df = DateFormat.getDateInstance(DateFormat.SHORT);
		System.out.println(df.format(date));//18-8-10

		df = DateFormat.getDateInstance(DateFormat.MEDIUM);
		System.out.println(df.format(date));//2018-8-10

		df = DateFormat.getDateInstance(DateFormat.LONG);
		System.out.println(df.format(date));//2018年8月10日

		df = DateFormat.getDateInstance(DateFormat.FULL);
		System.out.println(df.format(date));//2018年8月10日 星期五
//==Time=============================================================
		df = DateFormat.getTimeInstance();
		System.out.println(df.format(date));//11:50:46
		
		d1 = DateFormat.getTimeInstance(DateFormat.SHORT);
		System.out.println(d1.format(date));// 上午11:58

		d1 = DateFormat.getTimeInstance(DateFormat.MEDIUM);
		System.out.println(d1.format(date));// 11:50:46

		d1 = DateFormat.getTimeInstance(DateFormat.LONG);
		System.out.println(d1.format(date));// 上午11时58分30秒
//===DateTime=============================================================
		df = DateFormat.getDateTimeInstance();
		System.out.println(df.format(date));//2018-8-10 11:51:20

		df = DateFormat.getDateTimeInstance(DateFormat.FULL,DateFormat.FULL);
		System.out.println(df.format(date));//2018年8月10日 星期五 上午11时52分06秒 CST
		
//子类
		// yy MM dd  hh(12)  H   (24) mm ss S
		SimpleDateFormat sf1 = new SimpleDateFormat("yyyy年MM-dd H:mm:ss.SSS");
		System.out.println(sf1.format(date));	//2018年08-12 15:35:11.322
		
//-----------------------------8.0--------------------------------------
		//日期
		LocalDate ldate = LocalDate.now();
		ldate = LocalDate.of(2012, 1, 23);	//自己设置时间
		System.out.println(ldate);	//2012-02-23
		System.out.println(ldate.getYear());	//2012
		System.out.println(ldate.getMonthValue());	//2
		System.out.println(ldate.getDayOfMonth());	//23
		System.out.println(ldate.getDayOfYear());	//一年中的第几天54
		System.out.println(ldate.getDayOfWeek());	//THURSDAY
		System.out.println(ldate.plusYears(10));	//2022-02-23 10年后
		System.out.println(ldate.minusYears(5));	//2007-02-23 5年前
		
		//时间
		LocalTime ltime = LocalTime.now();
		ltime = LocalTime.of(3, 20, 23);	
		System.out.println(ltime);		//03:20:23
		System.out.println(ltime.getHour());	//3
		System.out.println(ltime.plusHours(10));	//13:20:23
		System.out.println(ltime.minusHours(4));	//23:20:23
		
		//日期时间
		LocalDateTime ldt1 = LocalDateTime.now();
		ldt1 = LocalDateTime.of(2012, 2, 3, 4, 5 ,56);
		System.out.println(ldt1);		//2012-02-03T04:05:56
		System.out.println(ldt1.getYear());	//2012
		System.out.println(ldt1.getDayOfMonth());	//3
		
		//运算
		LocalDateTime ldt2 = LocalDateTime.now();
		ldt2 = LocalDateTime.of(2012, 1, 3, 4, 5, 56);

		LocalDateTime ldt3 = LocalDateTime.now();
		ldt3 = LocalDateTime.of(2012, 2, 5, 4, 5, 56);

		Duration du = Duration.between(ldt2, ldt3);
		System.out.println(du.toDays());	//33
```

# 内部类

```
class Outer{//外部类 ,外围类
	class Inner {
		//内部类 ,嵌入类
	}

}
```

## 分类
1. 成员内部类
	1. 实例成员内部类
	2. 静态成员内部类
2. 局部内部类
3. 匿名内部类

## 成员内部类
### 实例成员内部类
1. 内部类可以使用四种权限访问修饰符
1. 内部类归该类的对象
	- 创建内部类对象
	- 外部类名.内部类名 实例名 = new 外部对象().new 内部类对象()
1. 内部类可以直接访问外部类成员，因为内部类对象中都隐藏了一个外部类对象
	- 包括private修饰的
1. 内部类字节码**命名:外部类名$内部类名.class**
	- Cat$CatBody.class
1. 除了 静态内部类 以外 ,都不允许 定义静态内容
  - 但是 static final 除外
  - static final int N=22;
  - 编译期能识别,编译后 所有N 用22 替代了
  -  因为static归类所用 ,内部类归外部类对象所有
1. **外部类静态方法**中**不能直接访问实例内部类数据**
	- 用对象调用 new Outer().new Inner().method();
	- **静态方法只能访问静态**
1. 如果外部类成员变量,内部类成员变量,内部类方法中的变量同名
	- 内部类方法中的变量> 内部类成员变量> 外部类成员变量
	- 内部类的方法访问同名变量
	- num:内部类方法中的变量
	- this.num:内部类成员变量
	- Other.this.num:内部类使用外部类成员变量
	- Other.this.fun():内部类调用外部类的成员方法


```
class Outer{
	public void f(){}
	int n;
	class Inner{
	
	}
}
```

例子:
```
class Cat {
	private int weight;

	public Cat() {
		super();
		// TODO Auto-generated constructor stub
	}

	public Cat(int weight) {
		this.weight = weight;
	}
----------------------------------------------------------------------------
class CatBody {

	*************************************************	
	1.class 可以用四种权限修饰符,
		* 如果为private的时候,就要提供公共的方法得到内部类对象
	
	*************************************************	

	private String color;

	public CatBody(String color) {
		super();
		this.color = color;
	}

	***********************************************************
	内部类可以直接访问外部类成员，因为内部类对象中都隐藏了一个外部类对象
	**********************************************************

	public void show() {
		System.out.println("颜色:" + color + ",重量" + weight);
		// System.out.println("颜色:"+color+",重量"+Cat.this.weight);
	}

}
---------------------------------------------------------------------------
	public void display() {
		System.err.println(weight);
		CatBody body = new CatBody("土豪金");
		body.show();
	}
}

===========================================================================

public class Demo7 {
	public static void main(String[] args) {
		Cat cat = new Cat(200);
		// 创建一个内部类对象
		Cat.CatBody body = cat.new CatBody("黑色");
		Cat.CatBody body2 = new Cat(120).new CatBody("金色");
		body.show();
		body2.show();
	}
}

===========================================================================
结果:
	颜色:黑色,重量200
	颜色:金色,重量120

```

### 静态内部类
1. 内部类是外部类的静态成员
1. 创建静态内部类对象
	- 外部类名.静态内部类名 = new 外部类名.静态内部类名();
1. 静态内部类的静态方法只能访问静态
1. 外部类使用静态内部类的静态方法
	- 通过静态内部类名.静态方法名();
1. 外部类使用静态内部类的成员方法'
	- 通过创建对象使用

```
class Outer{
     static void sf(){}
     static class Inner{
     }
 }
```

例子
```
class School {
-----------静态内部类-------------------------------------------------
	static class WaterFountain {
		private int x = 5000;
		static int y = 22;

		public void show() {
			System.out.println("水量" + x + "ml");
			System.out.println(y);
		}

		public static void show2() {

			System.out.println(y);
			// System.out.println(x);//静态方法不能调用非静态
			// System.out.println(z);//报错,静态方法不能调用非静态
			System.out.println(z1);

			System.out.println(new WaterFountain().x);//通过对象调用非静态
			System.out.println(new School().z);//通过对象调用非静态
		}
	}
--------成员变量----------------------------------------------------
	private int z = 100;
	private static int z1 = 100;

------成员方法--------------------------------------------------
	public void manager() {
		WaterFountain.show2();
		new WaterFountain().show();
	}

}
------测试-----------------------------------------------------
public class Demo9 {
	public static void main(String[] args) {
		//创建静态内部类对象
		//外部类使用静态内部类的成员方法
		School.WaterFountain w = new School.WaterFountain();
		w.show();

		//外部类使用静态内部类的静态方法
		School.WaterFountain.show2();
	}
}
```

### 接口中 可以定义静态内部类,静态内部接口
> 接口中 定义的 **类和接口默认 public static **

```
interface Info{
	//---静态内部类------------------------
	class A{
		int x = 11;
		public void f() {}
		public static void sf() {}
	}
	//---静态内部接口------------------------
	interface IA{
		int N = 45;
		void af();
		default void df() {}
		static void sf1() {}
	}
}


```

```
---接口的实现类----------------------------------
class InfoImpl implements Info {
//-----访问class A------------------------------------
	public void method() {
		// A是实现类的内部类,属于实现类的对象
		System.out.println(new A().x);
		new A().f();
		A.sf();		//静态方法属于类
	}

//-----访问接口内容-----------------------------------------------
	public void method2() {
		System.out.println(IA.N);
		IA.sf1();
		// 使用接口对象(匿名内部类)调用默认方法和抽象方法
		IA ia = new IA() {

			@Override
			public void af() {
				// TODO Auto-generated method stub

			}

		};

		ia.df();
		ia.af();
	}
}
```
> 实现接口中的接口

```
class IAImpl implements Info.IA {
	@Override
	public void af() {
	}

}

===使用===================================
public void show(){
	df();	//默认的用对象
	int n =Info.IA.N;	//静态的用接口名限定
	Info.IA.sf1();		//静态的用接口名限定
}
```

### 继承实例内部类

```
class Outer2{
	Outer2(){
		System.out.println("outer2");
	}
	class Inner2{
		Inner2(){
			System.out.println("Inner2");
		}
	}
}
```
> 继承实例内部类

1. 继承实例内部类
	1. 子类需要访问实例内部类(父类)的无参构造方法
2. 实例内部类是属于外部类的对象.
	1. 父类的构造方法是应该用外部对象调用(new Outer).
 
```
class Demo extends Outer2.Inner2{
	Demo(Outer2 o){
		o.super();
		//new Outer().super();
		System.out.println("Demo");
	}
}
public class TestInner {
	public static void main(String[] args) {
		Demo d = new Demo(new Outer2());
	}
}
```


## 局部内部类

注意：
- 局部内部类不能声明静态成员
- 局部内部类处于静态方法中只能直接访问外部类的静态成员
- 局部内部类处于实例方法中可以直接访问外部类静态成员和实例成员
- 局部类对局部变量的访问
	- 1.8 之前要用final修饰
	- 1.8 之后可以访问变量,但是变量的值不能更改
- 字节码文件名： **外部类名$N内部类名.class**

```
public class TestLocalInner1 {
	static int sn = 33;
	int num = 44;
	
	public void f() {
		int m = 45;//局部变量
		//1.8之前防止栈内存的数据在内部类使用的时候销毁,
		//需要用fianl修饰 final int m =45;
		//局部变量被用到的地方全部用值替换了.
		
		//非静态方法局部内部类
		class Parent{
			int x = 22;
			public void sss() {
				System.out.println(sn);	//可以访问静态和非静态
				System.out.println(num);
				//jdk1.8后可以访问局部变量了，但是变量的值 不能更改
				//1.8之前的
				System.out.println(m);
			}
		}
	}
	public static void main(String[] args) {
		//静态方法里的局部内部类
		class Parent{
			int x = 22;
			static final int n = 56;
			public void ss() {
				System.out.println(sn);		//静态只能范围内静态
				System.out.println(new TestLocalInner1().num);//访问非静态可以使用对象
			}
		}

//----局部内部类继承-------------------------------------------
		class Child extends Parent{
			int y = 33;
		}
		Child c = new Child();
		System.out.println(c.x);
		System.out.println(c.y);

	}

}

//---生成的字节码文件列表----------------------------------
TestLocalInner1.class
TestLocalInner1$1Parent.class
TestLocalInner1$2Parent.class
TestLocalInner1$1Child.class
```

## 匿名内部类
> 只能用一次
> 外部类名**$N.class**
> 理解: 匿名内部类就是**实现接口的类 或 继承了该类 的 对象**

格式
```
new 父类(参数列表) | 实现接口(){
	//匿名内部类的类体部分
}
```
**注意:**
- 必须实现一个类或一个接口。
	- 返回的是子类 或 实现类 的子类对象
- 不能定义静态数据
	- 只有静态内部类才能定义静态
	- static final 除外
- 不能定义构造器
	- 构造器和类名一样
	- 匿名就是没有名字
- 不能是抽象的
	- 返回的是继承了父类的子类对象
	- 抽象的就不能返回对象了
- 传参问题
	- 父类构造有参数,可以传参数
- 同名问题

```
interface Info1{
	void af();
}
interface IE{
	void f();
}
interface IF{
	void f();
}
```
```
//创建Info1的匿名对象
Info1 info1 = new Info1() {
		@Override
		public void af() {
			// TODO Auto-generated method stub
		}
	};
```

> 抽象类使用匿名内部类

```
abstract class Base1 {
	Base1(int n) {
		System.out.println(n);
	}

	public void f() {
	}
}

-----------------------------
Base1 base1 = 
		////---匿名内部类-------------------------
		new Base1(22) {
		{
			System.out.println("构造块");
		}

		public void f() {
			System.out.println("sub_f");
		}

		public void method() {
		}// 调用不到,Base1没有,多态
	};

//如果要调用,匿名内部类
//匿名内部类是对象,所以能调用方法
new Base1(44) {
		public void f() {
			System.out.println("sub_f");
		}

		public void m() {
			System.out.println("自己添加的m()");
		}
	}.m();
```
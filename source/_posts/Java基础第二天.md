---
title: Java基础第二天
date: 2018-7-30 9:07:22 
categories: JavaSE
tags: 
	- 变量
	- 数据类型
	- 运算符
top: 
---

# 存储数据--变量

## 变量
> 变量就是一个存储空间， 空间的数据是可变的。

<!-- more -->

声明变量： 数据类型 变量名（，变量名，变量名）；
	
	int age;

初始化：变量名 = 值； （开辟空间，赋初值）
	
	age=22;

边声明，变赋值
	
	int age = 22;

## 变量名命名规则

### 标识符命名规则

1. 由字母，数字，下划线_,美元符号$组成。
2. 不能以数字开头；
3. 不能是java的关键字；
4. 不能是字面值 ture false null
5. 字母可以看成所有国家书面上使用的，中文也可以看成字母。（不推荐）

### 关键字

保留字:没有特殊含义（goto ,const）

> 关键字如图

![](https://www.github.com/sun6145/githubPicture/raw/master/小书匠/JavaKeyWord.png "JavaKeyWord")

# java 数据数据类型

## 基本数据类型

|类型|内存（字节）大小|默认值|取值范围|
|:- | :-: |::-|-:|
|byte   |1 |0   |  -128 - 127&emsp;（-2^7)~（2^7-1）    |
|short  |2 |0   |  -32768（-2^15）~32767（2^15 - 1）     |
|int    |4 |0   |  -2,147,483,648（-2^31）~2,147,483,647（2^31-1）    |
|long   |8 |0   |  -9,223,372,036,854,775,808&emsp;~&emsp;9,223,372,036,854,775,807&emsp;（-2^63）~（2^63 -1）|
|float  |4 |0.0f|  float f1 = 234.5f|
|double |8 |0.0d|  double d1 = 123.4|
|char   |2 |单一的16位Unicode字符|\u0000~\uffff(0~65535)|
|boolean|1 |false| true/false|

### 测试

#### 整数

> int a =20; 
数据可以用下划线分隔 增强可读性
int n5=0b1011_1111_0000 //2进制 ：0b开头
int n6=01   //8进制：0开头
int 0x101f  //16进制：0x开头

#### 小数
> 小数字面值 当做 double 来处理
`double n1=11.1d;`   
`float n2 = 22.2f;`   
`double n3 = 2e2;`    //200.0


>十六进制 ：p以2为底  (e表示14)   
`double n1 = 0x1ap2;`&emsp;//0x1a `*` 2^2=0x1a `*` 4=26 `*` 4=104  
`ni = 6.0e20 + 1;&emsp;  `      //6.0E20,所以结果是不精确的

#### 小数的精确运算
1.导包：import java.math.BigDecimal;

```
BigDecimal n2 = new BigDecimal("0.1");
BigDecimal n3 = new BigDecimal("0.2");
BigDecimal addresult = n2.add(n3);
double result = addresult.doubleValue();
System.out.println(result);		//0.3
```

#### 字符（char)
> 计算机将字符是整数形式存储
编码表
1. ASCII
2. ISO-8859-1
3. Windows-1252
1. GBK2312（97%左右）
1. GBK(所有的,包含繁体）
1. GBk18030(所有的,包含繁体,少数民族）
1. Big(台湾香港繁体字)
2. Unicode编码(全世界统一，范围：0-65535)
    - UTF32（4字节，浪费空间）
    - UTF-16(双字节)
    - UTF-8（变长的，1,2,3字节）
	```
	String s = "Hello你好";
	byte[] b = s.getBytes("GBK"); // 以GBK编码存储
	String s1 = new String (b,"GBK");// 以GBK编码解码
	String s2 = new String (b,"utf-8");// 以Utf-8解码
	System.out.println(s1);	//Hello你好
	System.out.println(s2); // Hello???
	```

#### boolean 类型
> 字面值：
- true 
- false

## 引用类型

### java有 5种引用类型（对象类型）：

- 类 
- 接口 
- 数组 
- 枚举 
- 标注

> 引用类型：底层结构和基本类型差别较大

### JVM的内存空间：
（1）. Heap 堆空间：分配对象 new Student（）

（2）. Stack 栈空间：临时变量 Student stu

（3）.Code 代码区 ：类的定义，静态资源 Student.class

```
	eg：Student stu = new Student（）； //new 在内存的堆空间创建对象
	stu.study(); //把对象的地址赋给stu引用变量
	上例实现步骤：
		 a.JVM加载Student.class 到Code区
	     b.new Student()在堆空间分配空间并创建一个Student实例
	     c.将此实例的地址赋值给引用stu， 栈空间
```

## 字符串
> 用双引号引起来。

1. 字符串的连接:用“+”号连接
```
String name="张三";
System.out.println("姓名是："+name);	//姓名是：张三
```

## 类型转换

赋值的类型 和 声明的类型不一样

1. 自动类型转换
	> 由 低 （范围小的） 赋值给 高 （范围大的） 
	> 整形默认 int 
	> 小数默认 double    
	- byte --> short --> int --> long --> float --> double
	- char --> int --> long --> float --> double

	```
	byte a = 2;
	short b = a;
	System.out.println(b);	//2
	
	// b=ch+1; //char - short 不能转换
	
	//char类型参与运算
		char ch = 'a';
		int d = ch + 1;
		System.out.println(d);//a:97  结果98
	```

2. 强制类型转换
 	> 由 高 （范围大的）赋值给 低 （范围小的）
 	> (强转数据类型)（转换数据） 
 
3. 类型提升
	> 只有参与运算的时候才有类型提升
	> 类型低的提成到类型高的进行运算
	> 运算结果采取 类型级别最高的类型作为结果类型

# 运算符
1. 根据操作数个数分类： 
	- 一元	&emsp;//a++
	- 二元	&emsp;//a + b 
	- 三元	&emsp;//a>b?a:b

## 算法运算符

- \+ &emsp;&emsp;加法
	- 当+号左右有字符串的时候，+ 为字符串的连接符
- \- &emsp;&emsp;减法
- \* &emsp;&emsp;乘法
- /  &emsp;&emsp;除法
	1. 除数和被除数都是整数 ，结果为整数
	2. 除数为0,时，触发java.lang.ArithmeticException: / by zero
	3. 除数/被除数 为小数 的时候，结果为double类型
		```
		System.out.println(5/2.0);		//2.5
		System.out.println(5.0/2);		//2.5
		System.out.println(5.0/2.0);	//2.5
		```
	4. 浮点运算特殊值
		```
		System.out.println(0/0.0);		//NaN
		System.out.println(5/0.0);		//Infinity
		System.out.println(-5/0.0);		//-Infinity

		System.out.println(0.0/0);		//NaN
		System.out.println(5.0/0);		//Infinity
		System.out.println(-5.0/0);		//-Infinity
		```
	5. 取余
		```
		System.out.println(5%0);		//java.lang.ArithmeticException: / by zero
		System.out.println(5%2);		//1
		System.out.println(5%-2);		//1
		System.out.println(-5%2);		//-1
		System.out.println(5%0.0);		//NaN
		System.out.println(5%2.0);		//1.0
		System.out.println(-5%2.0);		//-1.0
		System.out.println(0.0%5);		//0.0
		System.out.println(-0.0%5);		//-0.0
		```

## 自增自减运算符

```
前自增和后自增、前自减和后自减的区别。
自增（++）：将变量的值加1，分前缀式（如++i）和后缀式（如i++）。
	前缀式是先加1再使用；
	后缀式是先使用再加1。
自减（--）：将变量的值减1，分前缀式（如--i）和后缀式（如i--）。
	前缀式是先减1再使用；
	后缀式是先使用再减1。
```

### j = i+++ i++

```
public static void main(String [] args){
        int i = 0;
        int j = i++ + i++;      //这条语句等价于j = (i++) + (i++)
        System.out.println("输出i的结果为"+i);
        System.out.println("输出j的结果为"+j);
    }
}

分析第一个（i++），假定 n=i++; 
	temp = n;
	i = i+1;
	n = temp;所以第一个（i++）==0；
第二个：因为是前缀式：(此时的i = 1)
  	所以最后的j = 1；
i自增了两次，所以 i = 2；
```

## 关系运算符
> &emsp;\> &emsp;< &emsp;>=&emsp; <=&emsp; == &emsp;!=   
> 运算结果是boolean类型

## 逻辑运算符
> && &emsp;|| &emsp; !

- &&
	- 一假为假
	- 全真为真
	- ***左边为假，后边不执行***
- ||
	- 一真为true
	- 全假为false
	- ***左边为真，后边不执行***
- ！
	- 真变假，假变真

### 非短路逻辑运算符　& | ！
> 不管左边为真为假，后面都要执行
> ***还可以进行位运算***

## 赋值运算符=
> 先算右边，然后将值给左边

### 扩展赋值
> += &emsp;-=&emsp;*=&emsp;/=
> 类型级别低 ，会自动强转处理

```
short n3 =5;
	n3 = n3 + 5;     //会报错 short = int		

	n3 += 5;		//不会报错，自动强转
	n3=(short)(n3+5);
```

## 条件运算符
格式：`表达式一 ？表达式二 : 表达式三；`  
	true ---> 表达式二
	false ---> 表达式三
	表达式一的结果 ***必须为boolean类型***
	结果必须接收或输出打印。

## 位运算符
- & 与
	1. 有一个为0，结果为0
	2. 二个都为1，结果为1
- | 或
	1. 有一个为1，结果为1
	2. 二个为0， 结果为0
- ^ 异或
	1. 相同为0， 不同为1
	2. 一个数异或二次，结果不变
- ~ 取反
	- 1 --- 0
	- 0 --- 1
	- `~a = -a -1`  &emsp;~5&emsp;--- &emsp;-6
- << 左移
	- 向左移动 几位，右边底位用0补位
	- 结果 * 2的 几位 次幂
- \>\> 右移
	- 向右移动 几位， 左边高位，是什么用什么补位
	- 结果 / 2的 几位 次幂
- \>>> 无符号右移
	- 向右移动 几位， 左边高位用0补位

	```
	int a=3;
	System.out.println(a<<2);		//3*4=12
	System.out.println(a>>1);		//3/2=1
	System.out.println(5>>>2);		//5/4=1
	int a1=-4;
	System.out.println(a1<<2);		//-4*4=-16
	System.out.println(a1>>1);		//-4/2=-2
	System.out.println(a1>>>28);	//15
	
			11111111 11111111 11111111 11111100
	>>>28	00000000 00000000 00000000 00001111
			------------------------------------
	```
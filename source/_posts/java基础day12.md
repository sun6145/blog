---
title: java基础day12
tags:
  - Lambda表达式
  - null
date: 2018-08-13 09:29:25
categories: JavaSE
top:
---
# 概述

函数式接口:接口中只有一个抽象方法

Lambda 只能实现函数式接口

<!-- more -->

具体用法参考:[Lanbda表达式](/2018/08/10/Lanbda表达式/ "Lanbda表达式")

## 方法传参

- 一个class的方法的形式参数是一个函数式接口,
- 这个时候可以传一个lambda 表达式
- 返回的对象必须是接口,强转为Object
- Object 0 =(Run) ()-> System.out.println("Hello.");

## 匿名内部类和 Lambda的区别
1. 
	- Lambda 只能实现函数式接口
	- 匿名内部类 可以是继承一个父类 或 实现 一个父类接口
2. 
	- Lambda 只能针对 函数式接口的抽象方法
	- 匿名内部类可以调用非抽象方法, 如 默认方法等.

# 方法引用
> 前提:Lambda 只有一行代码 ,只调用了一个方法

## 引用类方法(静态方法)
> 类名::类方法

```
interface La{
	Integer f(String s);
}

class Student{
	private String name;
	Student(String name){
		this.name = name;
	}
}
interface La1{
	Student f(String name);
}

=============================================

	//1.引用 类方法
	//	La la1 = s->{return Integer.parseInt(s);};
	La la1 = s -> Integer.parseInt(s);
	La la2 = Integer::parseInt;

	//2.特定对象 引用 实例方法
	La la3 = s->"hello".indexOf(s);
	La la4 = "hello"::indexOf;

	//3.某类对象 的实例方法
	La la5 = s->s.length();
	La la6 = String::length;

	//4.引用构造器
	La1 la7 = s->new Student(s);
	La1 la8 = Student::new;
```

## 引用实例(1.8下)
### 一元运算
> `static void parallelSetAll(int[] array, IntUnaryOperator generator)  `

```
//用 索引 替换掉 数组中的元素
int [] arr = {1,2,3,4,5};
Arrays.parallelSetAll(arr, new IntUnaryOperator() {
	
	@Override
	public int applyAsInt(int operand) {
		// 数组的索引
		return operand;
	}
});

//使用引用
Arrays.parallelSetAll(arr,index->index );

System.out.println(Arrays.toString(arr)); //[0, 1, 2, 3, 4]
```

### 二元运算
> `static void parallelPrefix(int[] array, IntBinaryOperator op)  `

```
//用 二元运算的结果 来替换掉数组的每一个元素
Arrays.parallelPrefix(arr, new IntBinaryOperator() {
	//     left -前一个元素     right:当前元素
	@Override
	public int applyAsInt(int left, int right) {
		// 当前元素是第一个元素  ，前一个元素  是1
		return left * right;
	}
});

//使用引用
Arrays.parallelPrefix(arr,(n1,n2)->n1*n2 );
System.out.println(Arrays.toString(arr)); //[1, 2, 6, 24, 120]
```

### 遍历数组 Stream流的方法
> `static IntStream stream(int[] array)  `

```
Arrays.stream(arr).forEach(new IntConsumer() {
	//value数组元素
	@Override
	public void accept(int value) {
		System.out.println(value);
	}
});

//Lambda
Arrays.stream(arr).forEach(num->System.out.println(num));
//方法引用
Arrays.stream(arr).forEach(System.out::println);
结果:
	1
	2
	3
	4
	5
```

# 异常
> 显示从开始到调用的位置
> 显示堆栈错误信息

- Throwable 异常的父类
	- Error 
		- **不能通过代码处理**
	- Exception
		-** 通过代码可以解决**
		1. 运行时异常 RuntimeException和它的子类
			1. 它的代码可以不用处理,但是出现异常会中断
		2. 非运行时的异常(受检异常):Exception类 和部分子类
			1. 在源代码中,必须进行异常处理,否则无法编译运行

## 常见的异常:

```
1.ArithemeticException
	x=5/0;

2. ArrayIndexOutofBoundsException
	int[] arr = {1,2};
	arr[2]=5;

3. NullPointerException
	String s = null;
	System.out.println(s.equals("abc"));//空指针
	//有些类能处理异常
	System.out.println(Objects.equals("abc"));//false不会报错
	System.out.println(Objects.hashCode(s));//不会报错

4. InputMismatchException:欲得到的数据类型与输入不匹配
	Scanner下控制输入不匹配

5. ClassCastException:对象强制类型转换出错
	Object o = new Object();
	Integer i = (Integer)o;

6. NumberFormatException: 数字格式化异常
	String s ="123a";
	int n = Integer.parseInt(s);

7. ClassNotFoundException: 不能加载所需的类
	ClassLoader.getSystemClassLoader().loadClass("包名.类名");
	
8. illegalArgumentException: 方法接收到非法参数
		 
```

## 异常的处理

### try-catch
> try 里面放可能会引发异常的代码
catch 放引发异常的类型

1. getMessage();获取异常的消息
2. printStackTrace()打印堆栈的错误信息

格式:
```
public void method(){
	try{
		//代码段（此处可能会产生异常_
	）catch(异常类型 ex){
		//对异常进行处理的代码段
	}
	//代码段
}
```


结果:
1. 没有引发异常
	1. 执行了try ,没有catch
2. 引发了异常类型的匹配
	1. 执行try ,也执行了catch
2. 出现了异常,但是异常类型不匹配
	1. 出现了中断

### 多重catch块
> 可以对不同类型的异常采取不同的处理方式
> 防止出现不知道的异常,加入父类
> 顺序:由子类到父类,由一般 到 特殊, 由常见 到 不常见

```
public void method(){
	try{
		//代码段（此处可能会产生异常_
	）catch(异常类型 ex){
		//对异常进行处理的代码段
	}catch(异常类型 ex){
		//对异常进行处理的代码段
	}
	
	....
	
	catch(异常类型 ex){
		//对异常进行处理的代码段
	}catch(异Exception e){
		//对异常进行处理的代码段
	}
}
```
例子:

```
public static void main(String[] args) {
	int [] arr = new int[5];
	Scanner input = new Scanner(System.in);
	System.out.println("赋值;");
	try {
		for(int i = 0; i < arr.length; i++) {
			arr[i] = input.nextInt();
		}
		Arrays.stream(arr).forEach(System.out::println);
	}catch(ArrayIndexOutOfBoundsException e) {
		System.out.println("下标越界");
	}catch(InputMismatchException e) {
		System.out.println("赋值出错了");
	}catch(Exception e) {
		System.out.println(e.getMessage());
	}finally {
		System.out.println("程序运行出错");
	}
	
	
}
```

#### 出现异常必须执行的代码
> 放入到finaly 的代码;除非虚拟机退出,否则一定会执行.
> 无论是否触发异常也会执行.
> 通常用来释放资源6

```
public void method(){
	try{
		//代码段（此处可能会产生异常_
	）catch(异常类型 ex){
		//对异常进行处理的代码段
	} finaly{
		//一定执行的代码段
	}
}
```


### 声明抛出throws
> 声明异常:给调用者处理
> 随调用谁处理

- 必须处理:Exception是受检异常,必须处理
	- 调用者处理: 
		- try-catch 
		- 抛给虚拟机(不推荐)
- 灵活处理:throws RuntimeException

> 抛出受检异常

```
public void f() throws Exception{
	Scanner input = new Scanner(System.in);
	int x = input.nextInt();
	int y = input.nextInt();
	int z = x / y;
	System.out.println(z);
}

try {
		new TestException5().f();
	} catch (Exception e) {
		// TODO Auto-generated catch block
		e.printStackTrace();
	}
```

> 抛出运行期异常

```
public void f() throws RuntimeException{
	Scanner input = new Scanner(System.in);
	int x = input.nextInt();
	int y = input.nextInt();
	int z = x / y;
	System.out.println(z);
}

//需要手动try-catch
try {
		new TestException5().f();
	} catch (RuntimeException e) {
		// TODO Auto-generated catch block
		e.printStackTrace();
	}
```

### 抛出异常
自己手动throw 抛出一个异常对象
- 抛出一个受检异常 ,方法必须要throws声明一下
	- 调用者必须处理.
- 抛出一个运行时异常, 不需要throws 声明

> 抛出受检异常

```
public void setAge(int age)throws AgeException {
	if(age >= 0 && age <= 150) {
		this.age = age;
	}else {
		//抛异常对象
		throw new Exception("年龄必须在 0 - 150之间");
	}
}

测试:
public static void main(String[] args) {
	
	Person zhangsan = new Person();
	try {
		zhangsan.setAge(250);
	} catch (Exception e) {
		// TODO Auto-generated catch block
		e.printStackTrace();
	}
}
```

## 自定义异常

```
class AgeException extends Exception{
	/*AgeException(String str){
		super(str);//调用父类构造传参
	}*/


	private String message;
	AgeException(String message){
		this.message = message;
	}
	@Override
	public String getMessage() {
		// TODO Auto-generated method stub
		return "message:" + message;
	}
	@Override
	public String toString() {
		// TODO Auto-generated method stub
		return "string:" + message;
	}
	@Override
	public void printStackTrace() {
		System.out.println("stack:" + message);
	}
	
}
```

```
class Person{
	private int age;

	public int getAge() {
		return age;
	}

	public void setAge(int age)throws AgeException {
		if(age >= 0 && age <= 150) {
			this.age = age;
		}else {
			//抛异常对象
			throw new AgeException("年龄必须在 0 - 150之间");
		}
	}
}
/////////////////////////////////////////////
public static void main(String[] args) {
	Person zhangsan = new Person();
	try {
		zhangsan.setAge(222);
	} catch (AgeException e) {
		e.printStackTrace();
		System.out.println(e);
		System.out.println(e.getMessage());
	}
}
/////////////////////////////////////////////
stack:年龄必须在 0 - 150之间
string:年龄必须在 0 - 150之间
message:年龄必须在 0 - 150之间
```
## 异常处理的原则
1. 只用于处理非正常的情况；

```
String [] arr = {"hello","hello","hello"};
//--正常的写法-------------------------------
for(int i = 0; i < arr.length; i++) {
		System.out.println(arr[i]);
	}

//--异常处理的写法---(不推荐)---------------------------	
try {
	for(int i = 0; ; i++) {
		System.out.println(arr[i]);
	}
} catch (Exception e) {
	
}
System.out.println("其他的代码");
}
```

2. 避免过大的try
3. 使用多重catch
3. 不要忽略catch块儿中的异常
3. 改正代码
3. 文档声明

```
/**
 * 
 * @param args
 * @throws Exception 
 */
```


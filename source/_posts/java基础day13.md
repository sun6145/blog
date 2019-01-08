---
title: java基础day13
tags:
  - 泛型
  - null
date: 2018-08-14 09:06:45
categories: JavaSE
top:
---

# 泛型
> 泛型，即“参数化类型”。就是将类型由原来的具体的类型参数化，类似于方法中的变量参数，此时类型也定义成参数形式（可以称之为类型形参），然后在使用/调用时传入具体的类型（类型实参）。

> 泛型的本质是为了参数化类型（在不创建新的类型的情况下，通过泛型指定的不同类型来控制形参具体限制的类型）。也就是说在泛型使用过程中，操作的数据类型被指定为一个参数，这种参数类型可以用在类、接口和方法中，分别被称为泛型类、泛型接口、泛型方法。

> **参考:[泛型使用](/2018/08/02/泛型/ "泛型")**

<!-- more -->


## 泛型使用
> `class Point<T>{}`:这是一个泛型类

```
1. <T>: 形式类型参数: 参数可以是任意类型

2. Point<具体的类型参数>:参数化类型
	应用时,一个类型后边指定一定具体的类型参数
	Point<Integer>

3. 类型推断 菱形语法
	Point<Integer> point = new Point<>();

4. 原生类型:
	使用的时候,类型后面没有指定具体类型
	Point point = new Point();

5. 类型参数: 可以定义多个类型参数,用逗号分隔
	Point<K , V>

6. W规范:
	1. 使用一个大写字母
		1. Type T
		2. Element E
		3. Key	K
		4. Value	v

7. 泛型的好处
	1. 编译期进行类型检查
	2. 类型不确定时也能使用

8. 格式
	class 类名<泛型类型标识>{
		访问修饰符 <泛型类型> 属性;
		访问修饰符 <泛型类型> 方法(){}
		访问修饰符 <泛型类型> 方法(泛型类型 参数){}
	}

应用：
	1. 类名<具体类型> 对象名 ＝ new 类名<具体类> ();
	2. 菱形语法
```

## 泛型通配符及上限、下限
- 通配符
	? 可以匹配任意的引用类型
- 泛型通配符上限
	? extends E:接收E或者E的子类型
- 泛型通配符下限
	? super E:接收E或者E的父类型

### 类型参数 与通配符的区别
1. 
	- 类型参数 可以表示一种类型
	- 通配符 不能表示一种类型
1. 
	- 类型参数只能指定上限
	- 通配符可以指定上限,也可以指定下限
1. 	
	- 类型参数可以指定多个上限,用`&`连接
	- 通配符只能指定一个上限

```
interface Info1 {}

interface Info2 {}

class Base {}

class Sub extends Base implements Info1, Info2 {
}

//===类型参数===========
class Point<T extends Base & Info1 & Info2> {}

//===通配符参数===========
class Point<T extends Number> {}
class Point<T super Integer> {}
```

> 多个参数化类型,生成的字节码文件是同一个
> 在生成字节码的时候,会擦除掉形式类型参数
> String 是 Object 的子类
> Object类型 = String类型 (可行)

- `Point<String>` **不是** `Point<Object>`的子类,泛型只是检查类型

## 泛型类举例
> 一个属性

```
class Apple<T> {
	private T size;

	public Apple(T size) { this.size = size;}

	public T getSize() { return size;}

	public void setSize(T size) { this.size = size;}

}

public class Demo2 {
	public static void main(String[] args) {
		Apple<Integer> apple = new Apple(10);
		apple.setSize(100);
		Apple<? super Integer> apple2 = new Apple(1.0);// Integer --Number --子类
		apple2.getSize();
		Apple<? extends String> apple3 = new Apple("123");// Integer --Number
		apple2.getSize();
	}
}
```

> 二个属性

```
class Contacts<K, V> {
	private K id;
	private V Tel;

	public Contacts(K id, V tel) {
		this.id = id;
		Tel = tel;
	}

	public K getId() {	return id;}

	public void setId(K id) {	this.id = id;}

	public V getTel() {	return Tel; }

	public void setTel(V tel) {	Tel = tel;}

}

public class Demo3 {
	public static void main(String[] args) {
		Contacts<?,String> contacts = new Contacts(1, "123456");
		System.out.println(contacts.getId()+"---"+contacts.getTel());
	}
}
```

## 泛型类使用总结:
1. 根据类的属性设置类型参数
	1. 参数只能指定上限(extend),不能下限
	2. 参数类型可以指定多个上限
2. 使用泛型类的时候 
	1. 使用通配符控制属性类型的范围

## 泛型构造器
> **使用泛型构造器的时候,不要使用菱形语法**

> 定义:
```
<E>  类名(E e){
	//代码块
}
```

> 使用:

1. `参数化类型 引用名 = new 泛型类名<>();`

2. <s>`参数化类型 引用名 = new <E>泛型类名<>();	//会报错`</s>
	`参数化类型 引用名 = new <E>泛型类名<`<i>具体类型</i>`>();`

> 例子
```
//定义泛型构造器

public <E> Demo(E e){
	System.out.println(e);
}

// 类型推断,根据参数推断
Demo<String> demo = new Demo<>(123);
	由123 得到<E> 是 Integer

Demo<String> demo2 = new <Integer> Demo<>(12);	//报错,不使用菱形语法
Demo<String> demo2 = new <Integer> Demo<String>("12");	//报错
	给定了<E>是Integer ,传入字符串"12"会报错

Demo<String> demo2 = new <Integer> Demo<String>(12);	//编译通过
Demo<String> demo3 = new <String > Demo<String>("12");	//编译通过
```


## 泛型方法
```
class Demo{
	//泛型方法定义
	public <T> void f(T t) {
		System.out.println(t);
	}

	public <T extends Object> T ff(T t) {
		//调用了泛型方法
		//类型推断
		f("hello");
		//显示指定具体的类型时  必须 用对象调用
		this.<String>f("hellotom");
		this.<Integer>f(123);
		return t;
	}
}
public class TestPoint3 {

	public static void main(String[] args) {
		Demo d = new Demo();
		//类型推断，根据参数推断
		d.f("hello");
		d.f(22);
		//
		d.<String>f("hellotom");
		d.<Double>f(22.2);
	}

}
```

# 泛型的擦除
> 泛型是编译的检查
> 生成的字节码的时候会擦除 

## 擦除原则：
- 对于参数化类型:`Point<String>`
	- 擦除后 为 原生类型:`Point`

- 对于类型参数:
	- 无界类型参数,擦除后为`object`
		- `private T a; ----->  private Object a;`
		- `<T> void f(T t) -----> void f( Object a);`
	- 有界:public 
		- 有一个上限
			- 用上限替换
		- 有多个上限
			- 用第一个上限来替换
		- `<T extends Number> void f(T t) -----> void f( Number a);`
		- `<T extends Base & Info1> void f(T t) -----> void f( Base a);`
		- `<T extends Info2 & Info1> void f(T t) -----> void f( Info2 a);`


## 泛型的重载
> 重载:参数名相同,参数列表不同


```
class Point1<T> {}

class Demo5 {
	
	public void f(Point1<String> p) {}  //Point t
	public void f(Point1<Integer> p) {} //Point t
-----擦除后一样,不能重载------------------------------

	<T> void f(T t) {}// Object t
	<T extends Number> void f(T t) {}// Number t
-----擦除后不一样,能重载------------------------------

	<T extends Base & Info1> void f(T t) {}// Base t
	<T extends Info1 & Info2> void f(T t) {}// Info1 t
-----擦除后不一样,能重载------------------------------
}
```


> 重写: 父类擦除后与子类参数相同

```
class Parent {

	// 父类擦除后与子类参数相同

	public void f(Point<String> p) {}// Point p

	class Child extends Parent {

		@Override
		public void f(Point p) {
		}
	}
}
```

## 泛型接口
实现泛型接口
	1. 知道类型的实现:(创建原生类)
		- `class 类名 implements 接口名<类型>` 
	2. 不知道类型的实现:(创建泛型类)
		- `class 类名<T> implements 接口名<T>`
```
//定义泛型接口
interface IA<T>{
	void af(T t);
}

// 1. 能确定类型
class IAImpl1 implements IA<String>{
	@override
	public void af(String t){
		System.out.println(t);
	}
}
// 1. 不能确定类型
class IAImpl2<T> implements IA<T>{
	@override
	public void af(T t){
		System.out.println(t);
	}
}

//测试
public static void main(String[] args) {
	IAImpl2<Integer> ia = new IAImpl2<>();
	ia.af(34);
	IAImpl2<String> ia1 = new IAImpl2<>();
	ia1.af("abc");
}
```

# 比较器
## Comparable 
> 自然排序使用
> 默认使用自然排序 ;升序
> 规则写在 int compareTo()方法,

> 使用
Comparable接口：让待排序对象所在的类实现Comparable接口，并重写Comparable接口中的compareTo()方法 
缺点是只能按照一种规则排序 

## Comparator : 外部比较器
> 自定义比较器使用

使用Comparator接口：编写多个排序方式类实现Comparator接口，并重写新`Comparator接口`中的`compare()`方法 
 * `public static <T> void sort(T[] a,Comparator<? super T> c)`,根据指定比较器产生的顺序对指定对象数组进行排序。数组中的所有元素都必须是通过指定比较器可相互比较的 
 * （也就是说，对于数组中的任何 e1 和 e2 元素而言，`c.compare(e1, e2) `不得抛出 `ClassCastException`）。 
 * 优点是可以按照多种方式排序，你要按照什么方式排序，就创建一个实现Comparator接口的排序方式类，然后将该排序类的对象传入到`Arrays.sort(待排序对象，该排序方式类的对象) `

## Comparable和Comparator区别：

1. 位置。
	- Comparable代码写在要比较的类中；
	- Comparator代码写在要比较的类外；

2. 个数。
	- Comparable就定义一种：
	- Comparator可以定义多个比较方式。

# 枚举
有的时候一个类的对**象是有限且固定**的，这种情况下我们使用枚举类就比较方便.
实际上 enum 就是一个 class，只不过 java 编译器帮我们做了语法的解析和编译而已。

## 枚举类
1. 语法
	```
	enum 枚举名称{
		对象,第一行处;
		构造;	//private
		方法();
		抽象方法();
			所有对象实现了抽象方法,那么此枚举类就是一个抽象枚举类
		重写方法;
		实现接口
			接口的方法要在对象中重写,(使用的是匿名内部类)
			生成了字节码文件
	}
	
	反编译: javap (-private) 包名.枚举名
	```
2. 注意事项
	1. 隐式的继承了 java.lang.Enum类;
	2. 对于一个非抽象的枚举类,**默认是final**的
	3. 枚举类的对象**必须** 在枚举类的**第一行代码处显示列举**出来
	4. 这些枚举对象都是 public static final;
	5. 枚举类的构造器都是私有privatae的
	6. 所有对象实现了抽象方法,那么此枚举类就是一个抽象枚举类

## 常用方法
1. `int compareTo(E o)` 
	比较此枚举与指定对象的顺序。

2. `Class<E> getDeclaringClass()` 
	返回与此枚举常量的枚举类型相对应的 Class 对象。

2. `String name()` 
	返回此枚举常量的名称，在其枚举声明中对其进行声明。

2. `int ordinal() `
	返回枚举常量的序数（它在枚举声明中的位置，其中初始常量序数为零）。

2. `String toString()`

	返回枚举常量的名称，它包含在声明中。

2. `static <T extends Enum<T>> T valueOf(Class<T> enumType, String name)` 
	返回带指定名称的指定枚举类型的枚举常量。

## 对象
> 必须在枚举类的第一行列举
> 对象都是 public static final;

```
enum Color {
	//枚举类的对象
	RED,BLUE,GREEN;
}
```

## 成员变量
```
enum Color  {
	private int no;
	private String name;
	public int getNo() {
		return no;
	}
	public void setNo(int no) {
		this.no = no;
	}
	public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}
}

//使用: 访问成员变量
Color.RED.setNo(11);
Color.RED.setName("红色");
System.out.println(Color.RED.getNo());
System.out.println(Color.RED.getName());
```

## 构造方法
> 构造方法是private的.
> 有构造方法,需要修改对象

```
enum Color implements InfoNew{
//对象--------------------
	RED(1,"红");


	//构造
	private Color(int no, String name) {
		this.no = no;
		this.name = name;
	}

	//成员变量
	private int no;
	private String name;
	public int getNo() {
		return no;
	}
	public void setNo(int no) {
		this.no = no;
	}
	public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}
}

//使用
//------------------构造赋值，访问成员变量-------------
System.out.println(Color.RED.getNo());
System.out.println(Color.RED.getName());
```

## 普通方法

```
enum Color  {
	public void show() {
		System.out.println("普通方法");
	}
}

//使用:对象都是静态的
Color.RED.show();
```

## 抽象方法
> 如果有抽象方法,对象必须实现抽象方法
> 实现了抽象方法(匿名内部类),那么此枚举类就是一个抽象枚举类

```
enum Color  {
RED(1,"红色") {
		@Override
		public void af() {
			System.out.println("实现了 RED的抽象方法");
		}
	};

	//抽象方法
	public abstract void af();
}

//-------------------抽象方法--------------------------------------
Color.RED.af();
```

## 实现接口
> 实现一个接口,枚举对象要重写抽象方法(匿名内部类)


```
interface InfoNew{
	void ff();
}
enum Color implements InfoNew {
//对象
	RED(1,"红色") {
		@Override
		public void af() {
			System.out.println("实现了 RED的抽象方法");
		}
	
		@Override
		public void ff() {
			System.out.println("实现了接口的抽象方法");
		}
	};
}
```

## 重写
> 重写`toString()`

```
enum Color{
	RED;
	@Override
		public String toString() {
			return no + "," + name;
		}
}

//------------------重写--------
System.out.println(Color.RED.toString());
```

## 枚举类的遍历
```
for(Color c : Color.values()){
	System.out.println(c.ordinal());
	System.out.println(c.name());
}
```

## switch使用
> switch(表达式)
> 表达式类型:byte short int char String enum
> 使用`枚举类对象=枚举类.valueOf(String str)`
> str不是枚举类的对象会抛出异常`IllegalArgumentException`

```
Scanner input = new Scanner(System.in);
System.out.println("输入颜色：");
String s = input.next();
Color cr = Color.valueOf(s);
switch(cr) {
	case RED:
		System.out.println("红色");
		break;
	case GREEN:
		System.out.println("绿色");
		break;
	case BLUE:
		System.out.println("蓝色");
		break;
}
```
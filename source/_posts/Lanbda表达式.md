---
title: Lanbda表达式
tags:
  - Lanbda表达式
  - null
date: 2018-08-10 22:19:05
categories: Java
top:
---
# Lambda表达式
- Lambda表达式（也称为闭包）是整个Java8发行版中最受期待的在Java语言层面上的改变
- Lambda允许把函数作为一个方法的参数（函数作为参数传递进方法中），或者把代码看成数据。
- Lambda表达式用于简化JAVA中接口式的匿名内部类。被称为函数式接口的概念。
- 函数式接囗就是一个**具有一个方法的普通接口**。像这样的接口，可以被隐式转换为lambda表达式。

<!-- more -->

## 格式
> (参数1,参数2....) -> {....};
1. 接口中抽象方法**只能有一个**(前提)
2. java 接口中的默认方法和镜头方法对lambda不影响
3. 当重写抽象方法只有一句的时候,可以省略大括号{}
4. 当重写方法只有一句返回值的代码块时,可以省略return和{},留下值

# 之前的处理
```
//定义只有一个抽象方法的接口
interface IEat {
	void eat();
}
```
## 方式一
**编写一个实现类**
```
class IEatImpl implements IEat {

	@Override
	public void eat(String thing) {
		System.out.println("吃苹果");
	}

}

-----测试:-----------------------------------
IEatImpl eat = new IEatImpl();
	eat.eat();
```

## 方式二
**匿名内部类**
```
IEat eat2 = new IEat() {
	 @Override
	 public void eat() {
	 System.out.println("匿名内部类重写eatd");
		}
	};
	eat2.eat();
```

## 使用Lambda表达式

```
IEat eat3 = () -> {
	System.out.println("Lambda重写eat方法");
 };

eat3.eat();

---简写模式(省略{})------------------
IEat eat4 = () -> System.out.println("Lambda重写eat方法");

eat3.eat();
```
# lambda的使用
>（1）没有参数时使用Lambda表达式
（2）带参数时使用Lambda表达式
（3）代码块中只一句代码时使用Lambda表达式
（4）代码块中有多句代码时使用Lambda表达式
（5）有返回值的代码块
（6）参数中使用fina关键字

## 没有参数时

```
IEat eat3 = () -> {
	System.out.println("Lambda重写eat方法");
 };

eat3.eat();

---简写模式(省略{})------------------
IEat eat4 = () -> System.out.println("Lambda重写eat方法");

eat3.eat();
```

## 带参数时
> 只有一个参数,类型可以省略,编译器可以推断出来(类型反推)
> 一个参数的时候,括号也可以省略


```
interface IEat {
	void eat(String thing);
}

IEat eat5 = (String thing) -> {
	 System.out.println("eat...." + thing);
	 };

eat5.eat("苹果");

----简写模式(省略类型)-------------------------------------
IEat eat5 = (thing) -> {
	 System.out.println("eat...." + thing);
	 };
```

> 多个参数的时候,类型也可以省略

```

interface IEat1 {
	void eat(String thing, String name);
}

IEat eat5 = (String thing, String name) -> {
	 System.out.println(name + "eat...." + thing);
	 };

eat5.eat("苹果","我");
```

## 代码块中只一句代码
> 可以省略{}
```
IEat eat5 = (thing) -> System.out.println("eat...." + thing);
```

### 代码块中有多句代码时
> {}大括号不可以省略
```
IEat eat5 = (thing) -> {
			System.out.println("吃东西了.");
			System.out.println("eat...." + thing);
		};
```

## 有返回值
- 只有return 返回值一句代码
	- {}和return 都省略
	- 保留{} 和 return
	- 只要留下的结果是一个值即可,(三目运算符)
```
interface IEat1 {
	int eat(String thing, String name);
}
 
----一句代码-------------------------------------
IEat1 eat6 = (name, thing) -> 10;			//return 10
IEat1 eat6 = (name, thing) -> return 10;	//编译不能通过

IEat1 eat6 = (name, thing) -> 5 > 3 ? 100 : -1;		//三目运算符

----多行代码----------------------------------
IEat1 eat6 = (thing, name) -> {
	System.out.println(name + "吃了" + thing);
	return 10;
};
```
## 参数中使用fina关键字
- 表达式中可以不添加
- 如果要添加final关键字
	- 带上数据类型

```
interface IEat3 {
	int eat(final String thing, final String name);

	public default void show() {				//默认方法
		System.out.println("show");
	}

	public static void method() {				//静态方法
		System.out.println("method");
	}
}

--第一种----------------------------------
IEat3 eat7 = (thing,name)->{
	System.out.println(name + "吃了" + thing);
	return 10;
};

--第二种----------------------------------
IEat3 eat7 = (final String thing, final String name) -> {
	System.out.println(name + "吃了" + thing);
	return 10;
};

```
> 调用接口中的静态方法:接口调用
```
IEat3.method();
```

> 调用接口中的默认方法:实现接口的对象调用
```
new IEat3() {
			
	@Override
	public int eat(String thing, String name) {
		return 0;
	}
}.show();

---lanbda--------------------------------
IEat3 i = (thing, name) -> 0;
i.show();
```
# 应用于排序

```
public class Student {
	private int age;
	private String name;

	public Student() {
		super();
		// TODO Auto-generated constructor stub
	}

	public Student(int age, String name) {
		super();
		this.age = age;
		this.name = name;
	}

	public int getAge() {
		return age;
	}

	public void setAge(int age) {
		this.age = age;
	}

	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}

	@Override
	public String toString() {
		return "Student [age=" + age + ", name=" + name + "]";
	}

}
```
## 对学生排序
```
public static void main(String[] args) {
	Student[] students = { new Student(18, "叶红"), new Student(15, "疏风"), new Student(21, "撒旦法") };

	// 第一种:匿名内部类实现
	Arrays.sort(students, new Comparator<Student>() {
	
	 @Override
	 public int compare(Student o1, Student o2) {
	 int num = o1.getAge() - o2.getAge();
	 int num2 = num == 0 ? o1.getName().compareTo(o2.getName()) : num;
	 return num2;
	 }
	 });

	// 遍历
	for (Student s : students) {
		System.out.println(s.getName() + "---" + s.getAge());
	}
}
```
## 使用lambda对学生排序
```
public static void main(String[] args) {
	Student[] students = { new Student(18, "叶红"), new Student(15, "疏风"), new Student(21, "撒旦法") };

	Comparator<Student> c = (o1, o2) -> {
		int num = o1.getAge() - o2.getAge();
		int num2 = num == 0 ? o1.getName().compareTo(o2.getName()) : num;
		return num2;
	};

	Arrays.sort(students, c);

	
----简便写法-------------------------------------------------------------------------
	Arrays.sort(students, (o1, o2) -> {
		int num = o1.getAge() - o2.getAge();
		int num2 = num == 0 ? o1.getName().compareTo(o2.getName()) : num;
		return num2;
	});
-----------------------------------------------------------------------------------

	// 遍历
	for (Student s : students) {
		System.out.println(s.getName() + "---" + s.getAge());
	}
}
```


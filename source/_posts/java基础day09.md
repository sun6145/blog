---
title: java基础day09
tags:
  - final
  - 抽象类
  - 接口
  - 关键字
date: 2018-08-08 09:16:36
categories: JavaSE
top: 
---
# final 终态
- 类 ： 此类 不能被继承
- 方法： 此方法不能被重写
- 变量： 此变量的值不能更改，是常量

<!-- more -->

final 类：
1. 为了类的安全
2. 类中的方法有复杂的调用关系
3. 是最终版本的类

final 方法：
1. 不希望子类重写
2. 使用统一的父类版本

final 变量：
1. 自定义常量： 如PI,
	- ` final 数据类型 变量名 =值`
	- 常量的变量名 大写 ，多个单词用_连接
2. 作用
	1. 安全性，防止运算的值的修改
	2. 便于维护
	3. 增强可读性


# 继承设计的原则
1. 继承的层次 最好 2~3层。
2. 精心的用于继承的类
	1. 提供文档说明
	1. 隐藏类中的实现细节

# 继承的缺点
1. 破坏封装
2. 高耦合，便于维护，父类修改，子类也修改了

# 类与类组合 
- 类和类 之间的关系： 整体和局部
- 局部类作为成员变量&emsp;&emsp;Heart h = new Heart();//成员变量

```
package day9；

//心脏 局部类
class Heartl{
	public void beat(){
		System.out.printin(“心跳“）；
	}
}

//狼 整体类
class Wolf1{
	Heart h=new Heart()；//成员变量
	public void run();
	System.out.println("狼跑”）；
}

public class TestAnimalf{
	public static void main(String[]args){
		Wolf1 wolf=new Wolf1()；
		wolf.h.beat()；
		wolf.run（）；
	}
}
```


# 抽象类
> 只约定类所具有的抽象行为，没有具体实行对应的行为

格式
```
abstract class 类名{

	常量；

	变量；

	访问修饰符 abstract 返回类型 方法名（）；//抽象方法

	普通方法；

	静态方法（）；
}
```

## 注意
1. 抽象类 不能 创建对象
1. 抽象方法不是必须定义
1. 带有抽象方法类的必须是抽象类
1. 构造方法 不能是抽象的；
1. abstract 不能与 private ,final,static
	- private 不能被继承，就不能重写
	- final 不能被重写
	- static ,方法没有方法体，无意义

## 抽象方法
1. 没有方法的实现
	- 没有{（空实现）}，
1. 子类必须实现父类的所有抽象方法
	- 重写父类的抽象方法
	- 抽象子类除外

## 构造方法
- 构造方法不能是抽象的,
	- 构造方法不能被继承
	- 抽象的方法要子类重写，矛盾

> 虽然抽象类不能被实例化，但可以有构造函数。由于抽象类的构造函数在实例化派生类之前发生，所以，可以在这个阶段初始化抽象类字段或执行其它与子类相关的代码。

## 抽象类使用场合：
1. 此类 不适合创建对象；
2. 此类 提供一个模板的作用；

# 接口
> 是一组 公开的 规则 是功能的组合。

## 接口注意：
1. 不能创建对象（抽象）
2. 普通的实现类 要 实现接口中的 所有抽象方法
	- 抽象的实现类除外
3. 实现类 可以实现 多个接口
4. 接口之间 可以 多继承
5. 接口不能实现接口

```
访问修饰符 interface 接口名{
 	静态的常量；public  ,static  final
	抽象方法；public abstract
	默认方法；(1.8后支持)default,可以有方法体
	静态方法；(1.8后支持)static
	静态内部类型；public static 内部类，内部接口， 内部枚举enum
}
一个类中只能，接口不能用public 修饰
```

## 接口的特点：
1. 一组 公开 规则
2. 降低了类之间的耦合连接
3. 扩充了 功能实现

## 扩展方法
### 默认方法
> 使得接口在进行扩展的时候，不会破坏与接口相关的实现类代码
> default方法，只能通过接口实现类的对象来调用。

- 之前开发中，接口实现了就不能更改，有新的需求，
	- 写一个新的接口，
	- 继承之前的接口，
	- 添加新功能，
	- 实现该类，
	- 调用新类。
- 1.8之后，可以在原来的接口中，
	- 写入新的默认方法，
	- 调用默认方法。  

#### 接口多实现下的问题

> 一个类实现了两个接口（可以看做是“多继承”），这两个接口又同时都包含了一个名字相同的default方法f2()
> 编译器会报错,
> 在编译阶段报错要求开发者必须override同名的方法

```
interface C extends A,B{
	@Override
	default void f2() {
		B.super.f2();		//重写B接口的f2()方法
		B.super.f2();		//重写A接口的f2()方法
	//可以使用内部类的分别重写A,B
	}
}
```
##  静态方法
> 只能通过接口名调用，不可以通过实现类的类名或者实现类的对象调用。

```
interface A {
	public default void  f2(){
		 System.out.println("A接口中的默认方法");
	}
	public static  void f() {
		System.out.println("A_静态方法！");
	}
}

interface B {
	public default void  f2(){
		 System.out.println("B接口中的默认方法");
	}
	public static void f() {
		System.out.println("B_静态方法！");
	}
}
==============重写同名的默认方法====================
interface C extends A,B{
	@Override
	default void f2() {
		B.super.f2();		//重写B接口的f2()方法
		B.super.f2();		//重写A接口的f2()方法
	//可以使用内部类的分别重写A,B
	}
}
==============测试================
public class Test implements C{
	public static void main(String[] args) {
		A.f();		//接口静态方法 通过接口名调用
		B.f();		//接口静态方法 通过接口名调用
		Test d1 = new Test();	//接口默认方法 通过接口实现类的对象调用
		d1.f2();
	}
}

```

# 接口和抽象类的区别
## 相同
1. 都是引用数据类型
2. 都不能创建对象
3. 都能定义抽象方法
4. 都是 用 子类继承 或 实现类 实现（重写） 来 使用。

## 不同
1. 类是单一继承的
2. 接口的实现类可以实现多个接口的功能
3. 接口之间可以多继承
 
## 二者的理解
### 抽象类
1. 为所有子类提供一个模板，半成品
2. 子类在 父抽象类的 基础上扩展

### 接口：
1. 提供一组公开的规则
2. 
	- 同一个应用程序，接口将各个类（模块）耦合连接，（不使用继承）
	- 对于不同的应用， 接口就是通信的规则

> 1. 比如鸟有fly（）；鸵鸟extends 鸟，（高耦合，父类有的子类也要有），
> 2. 鸵鸟也能飞，（设计不合理）
 	1. 写一个fly()接口，
 	2. 鸵鸟 extends 鸟 implements flyInterface;

> 商城实现支付接口，就能与银行进行通信

# 多态
> 一种物质，多种形态

- 运行时多态：父类引用： 指向 -》 子类对象
- 编译器多态：右边：方法的重载
- 接口多态： 接口指向接口的实现类

## 多态的特点：
1. 方法重写（前提）
2. 父类引用： 指向 -》 子类对象
3. 接口引用: 执向 -》 实现类对象

## 多态中的类型转换
1. 子类转父类: 向上类型转换
2. 父类转子类: 向下类型转换
	- 需要强制转换
	- 注意类型不匹配:java.lang.ClassCastException 

### 运算符:instanceof
> **判断某个对象 是否是 类 或 接口类型，**
>  **是 true,不是  false**

pet instanceof Cat//判断传递的pet对象是不是Cat类类型

```
class Pet {
	private String name;

	public Pet(String name) {
		this.name = name;
	}

	public String getName() {
		return name;
	}

	public void eat() {
		System.out.println("吃东西");
	}
}
=======================================================
class Dog extends Pet {
	public Dog(String name) {
		super(name);
	}

	@Override
	public void eat() {
		System.out.println("吃骨头");
	}

	public void run() {
		System.out.println("小狗跑了");
	}
}
=======================================================
class Cat extends Pet {
	public Cat(String name) {
		super(name);
	}

	public void eat() {
		System.out.println("吃鱼");
	}

	public void play() {
		System.out.println("小猫去玩儿了");
	}
}
=======================================================
class PetHospatil {
	public void treatment(Pet pet) {//类与类依赖关系
		// Pet pet = wangwang; Dog - > Pet 向上类型转换
		System.out.println("给" + pet.getName() + "看病");
		pet.eat();
		// 对类型进行判断//
		if (pet instanceof Dog) {
			Dog dog = (Dog) pet;
			dog.run();
		}
		if (pet instanceof Cat) {
			Cat cat = (Cat) pet;
			cat.play();
		}
	}
}
=======================================================
public class TestHospatil {
	public static void main(String[] args) {
		PetHospatil hos = new PetHospatil();
		Dog wangwang = new Dog("旺旺");
		Cat xiaohua = new Cat("小花");
		hos.treatment(wangwang);
		hos.treatment(xiaohua);

	}

}
```

# 类与类的关系
- 组合:局部和整体
- 继承:泛化
- 依赖:
	- 类A要完成某个功能引用了类B，则类A依赖类B。依赖在代码中主要体现为类A的某个成员方法的返回值、形参、局部变量或静态方法的调用，则表示类A引用了类B。
	- 医院治疗功能依赖宠物,医院的方法中接收的参数是宠物类型,
	- 多态,传递的是宠物的子类对象
	- 依赖谁,谁就是参数
[认识UML类关系——依赖、关联、聚合、组合、泛化](https://blog.csdn.net/K346K346/article/details/59582926 "认识UML类关系——依赖、关联、聚合、组合、泛化")
---
title: List接口
tags:
  - List接口的成员方法
  - List 集合的遍历
date: 2018-07-31 17:27:55
categories: Java
top:
---
# List 接口概述（列表）
> 有序的collection，可以对列表中的每个元素的插入位置进行精确的控制。用户可以根据元素的整数索引（在列表中的位置）访问元素，并搜索列表中的元素。   
> 列表通常允许重复的元素。

<!-- more -->

## List的存储和遍历
### List存储字符串并遍历
```
//创建集合对象
List list = new ArrayList();

//创建字符串并添加
list.add("Hello");
list.add("world");
list.add("java");

//遍历集合
Iterator it = list.iterator();
while(it.hasNext()){
	String s = (STring)it.next();
	System.out.println(s);
}
```

### List存储自定义对象并遍历
```
// 创建集合对象
List list = new ArrayList();

//创建学生对象
Student s1 = new Student("苏伯陵",27);
Student s2 = new Student("东方瑾",26);
Student s3 = new Student("夜子宸",28);

//把学生对象添加到集合中
list.add(s1);
list.add(s2);
list.add(s3);

//遍历集合
Iterator it = list.terator();
while(it.hasNex()){
	Student s = (Student)it.next();
	System.out.println(s.getName + "---" + s.getAge());
}
```

## List集合的特点：
**有序(存储和取出的元素一致)，可重复的。**
```
// 创建集合对象
List list = new ArrayList();

// 存储元素
list.add("hello");
list.add("world");
list.add("java");
list.add("javaee");
list.add("android");
list.add("javaee");
list.add("android");

// 遍历集合
Iterator it = list.iterator();
while (it.hasNext()) {
	String s = (String) it.next();
	System.out.println(s);
}
结果：
	hello
	world
	java
	javaee
	android
	javaee
	android
```

## List 接口的成员方法
1. 添加功能
	- void add(int index,E element):&emsp;**在指定位置添加元素**
2. 获取功能
	- E get(int index):&emsp;**获取指定位置的元素**
3. 删除功能
	- E remove(int index):&emsp;**根据索引删除元素,返回被删除的元素**
4. 修改功能
	- E set(int index,E element):&emsp;**根据索引修改元素，返回被修饰的元素**
5. 列表迭代器
	- ListIterator listIterator():&emsp;**List集合特有的迭代器**

# List的子类

## ArrayList类概述及使用
ArrayList类概述
底层数据结构是数组，查询快，增删慢
线程不安全，效率高

```
//创建list对象
List list = new ArrayList();

//添加元素
list.add("hello");
list.add("world");
list.add("java");

----指定位置添加元素---------------------------------------
list.add(1,"android");
System.out.println("list:" + list);			//list:[hello, android, world, java]
	list.add(4, "javaee");					//有问题 IndexOutOfBoundsException
	list.add(3, "javaee"); 					//没有问题,在最后添加list:[hello, world, java, javaee]

----获取指定位置的元素--------------------------------------
System.out.println("get:" + list.get(1));	//get:world
System.out.println("list:" + list);			//list:[hello, world, java]
System.out.println("get:" + list.get(3));	//IndexOutOfBoundsException


----索引删除元素，返回被删除的元素--------------------------------------
System.out.println("remove:" + list.remove(1));		//remove:world
System.out.println("list:" + list);					//list:[hello, java]
System.out.println("remove:" + list.remove(3));		// IndexOutOfBoundsException

----索引修改元素，返回被修饰的元素--------------------------------------
System.out.println("set:" + list.set(1, "javaee"));	//set:world
System.out.println("list:" + list);					//list:[hello, javaee, java]
```

### ArrayList 自带方法的遍历
#### 普通for
##### 遍历数组 size() 和 get() 方法
```
//创建lis对象
List list = new ArrayList();

//添加元素
list.add("hello");
list.add("world");
list.add("java");

//遍历
for(int i = 0;i < list.size(); i++){
	String s = (String) list.get(i);
	System.ou.println(s);
}
结果：
	hello
	world
	java
```

##### 遍历自定义对象 size()和get()结合
```
// 创建集合对象
List list = new ArrayList();

//创建学生对象
Student s1 = new Student("苏伯陵",27);
Student s2 = new Student("东方瑾",26);
Student s3 = new Student("夜子宸",28);

//把学生对象添加到集合中
list.add(s1);
list.add(s2);
list.add(s3);

//遍历集合
for(int i =0; i<list.size();i++){
	Student s = (Student)list.ger(i);
	System.out.println(s.getName + "---" + s.getAge());
}

```

#### **列表迭代器**
> **ListIterator listIterator()：List集合特有的迭代器**
	- 该迭代器继承了Iterator迭代器，所以，就可以直接使用hasNext()和next()方法。
> **特有功能：**
	- Object previous():获取上一个元素
	- boolean hasPrevious():判断是否有元素

##### 迭代器遍历数组
```
// 创建List集合对象
List list = new ArrayList();
list.add("hello");
list.add("world");
list.add("java");

// 集合的遍历
// 创建迭代器
ListIteartor lit = list.listIterator();	// 子类对象

while (lit.hasNext()) {
	String s = (String) lit.next();
	System.out.println(s);
}

System.out.println("-----------------");

while (lit.hasPrevious()) {
	String s = (String) lit.previous();
	System.out.println(s);
}

结果：
	hello
	world
	java
	-----------------
	java
	world
	hello
```

#### 修改集合的内容
> 我有一个集合，如下，请问，我想判断里面有没有"world"这个元素，如果有，我就添加一个"javaee"元素，请写代码实现。

```
List list = new ArrayList();
// 添加元素
list.add("hello");
list.add("world");
list.add("java");

ListIterator lit  = list.listIterator();
while(lit.hasNext()){
	if(lit.next().equals("world")){
		list.add("javaEE");
	}
}
```
#### <center>**↑，ConcurrentModificationException:当方法检测到对象的并发修改，但不允许这种修改时，抛出此异常。**</center>
> ![](https://www.github.com/sun6145/githubPicture/raw/master/小书匠/what.jpg )

#### 产生问题的原因

> 迭代器依赖集合而存在，在判断成功后，集合中的新添加了元素，而迭代器却不知道，这个错叫并发修改异常。
* 其实这个问题描述的是：迭代器遍历元素的时候，通过集合是不能修改元素的。

#### 解决办法
1. **迭代器迭代元素，迭代器修改元素**
	元素是跟在刚才迭代的元素后面的。    
	而Iterator迭代器却没有添加功能，所以我们使用其子接口ListIterator
	ListIterator:
		- void add();
		- void remove();
		- void set(E e);
```
List list = new ArrayList();
// 添加元素
list.add("hello");
list.add("world");
list.add("java");

//迭代器遍历修改
ListIterator lit =list.listIterator();
while(lit.hasNext()){
	String s = （String)lit.next();
	if(s.equals("world")){
		lit.add("javaee");
	}
}
```

2. **集合遍历元素，集合修改元素(普通for)**
	元素在最后添加的。
```
List list = new ArrayList();
// 添加元素
list.add("hello");
list.add("world");
list.add("java");

//集合遍历修改
for(int i =0;i<list.size()；i++){
	String s = （String)list.get(i);
	if（"world".equals(s)）{
		list.add("javaee");
	}
}
```

## Vector类概述
底层数据结构是数组，查询快，增删慢
线程安全，效率低

### Vector的特有功能
1. 添加功能
	* public void addElement(Object obj)&emsp;&emsp;&emsp;&emsp;--	add()
2. 获取功能
	- public Object elementAt(int index)&emsp;&emsp;&emsp;&emsp;		--  get()
	- public Enumeration elements()&emsp;&emsp;&emsp;&emsp;			--	Iterator iterator()
	- boolean hasMoreElements()&emsp;&emsp;&emsp;&emsp;				hasNext()
	- Object nextElement()&emsp;&emsp;&emsp;&emsp;					next()

> 一般不用特有的功能，用后面的方法替代，添加遍历与ArrayList相似

## LinkedList类概述
底层数据结构是链表，查询慢，增删快
线程不安全，效率高

### LinkedList类特有功能
1. 添加功能
	- public void addFirst(E e)及addLast(E e)
1. 获取功能
	- public E getFirst()及getLast()
1. 删除功能
	- public E removeFirst()及public E removeLast()

```
// 创建集合对象
LinkedList link = new LinkedList();

// 添加元素
link.add("hello");
link.add("world");
link.add("java");

// public void addFirst(Object e)
// link.addFirst("javaee");
// public void addLast(Object e)
// link.addLast("android");

link:[javaee, hello, world, java, android]

-------------------------------------------------

// public Object getFirst()
// System.out.println("getFirst:" + link.getFirst());
// public Obejct getLast()
// System.out.println("getLast:" + link.getLast());

getFirst:hello
getLast:java
link:[hello, world, java]

-------------------------------------------------------

// public Object removeFirst()
System.out.println("removeFirst:" + link.removeFirst());
// public Object removeLast()
System.out.println("removeLast:" + link.removeLast());

removeFirst:hello
removeLast:java
link:[world]

---------------------------------------------------------
// 输出对象名
System.out.println("link:" + link);
```

# List的子类特点

|子类|底层数据结构|特点|安全|效率|
|:-:|:-:|-:|-:|-:|
|ArrayList|数组|查询快，增删慢|线程不安全|效率高|
|Vector|数组|查询快，增删慢|线程安全|效率低|
|LinkedList|链表|查询慢，增删快|线程不安全|效率高|


		
	List有三个儿子，我们到底使用谁呢?
		看需求(情况)。
		
	要安全吗?
		要：Vector(即使要安全，也不用这个了，后面有替代的)
		不要：ArrayList或者LinkedList
			查询多：ArrayList
			增删多：LinkedList
			
	如果你什么都不懂，就用ArrayList。


# 数据结构

> [常见数据结构与算法整理总结（上）](https://www.jianshu.com/p/230e6fde9c75)

> [数据结构之数组和链表](https://www.github.com/sun6145/githubPicture/raw/master/小书匠/数据结构之数组和链表.png "数据结构之数组和链表")

> [数据结构之栈和队列](https://www.github.com/sun6145/githubPicture/raw/master/小书匠/数据结构之栈和队列.png "数据结构之栈和队列")
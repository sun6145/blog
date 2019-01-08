---
title: Set接口
tags:
  - HashSet
  - LinkedHashSet
  - TreeSet
date: 2018-08-03 15:11:06
categories: Java
top:
---
Collection
	- List :有序(存取的顺序一致),可重复
	- Set  :无序(存储顺序和取出顺序不一致),唯一
		- HashSet :底层是哈希表结构保证唯一
		- LinkedHashSet :由链表保证有序,由哈希表保证唯一
		- TreeSet:底层数据结构是红黑树(自平衡二叉树),排序和唯一

<!-- more -->

# HashSet集合
> 底层是哈希表结构。
> 哈希表结构底层依赖:hashCode()和equals()方法。
> 如果你认为对象的成员变量值相同即为同一个对象的话，你就应该重写这两个方法。
> 它不保证set的迭代顺序;特别是它不保证该顺序恒久不变
> 虽然Set集合的元素无序,但是,作为集合来说,它肯定有自己的存储顺序
> 当=存储顺序和它的存储顺序一致时,代表不了有序,可以多存储一些数据,就能看到效果.

```
// 创建集合对象
Set<String> set = new HashSet<String>();

// 添加元素
set.add("hello");
set.add("java");
set.add("world");
set.add("java");
set.add("world");

// 遍历
// 方式一:迭代器
Iterator<String> it = set.iterator();
while (it.hasNext()) {
	String s = it.next();
	System.out.println(s);
}
//方式二:
for(String s : set){
	System.out.println(s);
}
结果:
	java
	world
	hello
// 无序 唯一
```

## 存储字符串并遍历
```
// 创建集合对象
HashSet<String> hs = new HashSet<String>();

// 创建并添加元素
hs.add("hello");
hs.add("world");
hs.add("java");
hs.add("world");

// 遍历集合
for (String s : hs) {
	System.out.println(s);
}
```

## 如何保证唯一
>  通过查看add方法的源码，我们知道这个方法底层依赖 两个方法：hashCode()和equals()。
>   步骤：
 * 		首先比较哈希值
 * 		如果相同，继续走，比较地址值或者走equals()
 * 		如果不同,就直接添加到集合中	

> 先看hashCode()值是否相同
 * 相同:继续走equals()方法
 	* 返回true：	说明元素重复，就不添加
 	* 返回false：说明元素不重复，就添加到集合
 * 不同：就直接把元素添加到集合
 * 如果类没有重写这两个方法，默认使用的Object()。一般来说不同相同。
 * 而String类重写了hashCode()和equals()方法，所以，它就可以把内容相同的字符串去掉。只留下一个。

## hashCode()方法理解
- 由于成员变量值影响了哈希值，所以我们把成员变量值相加即可
this.name.hashCode() + this.age;

- 可能出现下面情况,对象不同,但hashCode一样,会进入equals方法
// s1:name.hashCode()=40,age=30
// s2:name.hashCode()=20,age=50

- 尽可能的区分,我们可以把它们乘以一些整数
```
@Override
public int hashCode() {
	final int prime = 31;
	int result = 1;
	result = prime * result + age;
	result = prime * result + ((name == null) ? 0 : name.hashCode());
	return result;
}
```
 
```
System.out.println("hello".hashCode());		//99162322
System.out.println("hello".hashCode());		//99162322
System.out.println("world".hashCode());		//113318802
```

## equals方法
```
@Override
public boolean equals(Object obj) {
	if (this == obj)
		return true;
	if (obj == null)
		return false;
	if (getClass() != obj.getClass())
		return false;
	Student other = (Student) obj;
	if (age != other.age)
		return false;
	if (name == null) {
		if (other.name != null)
			return false;
	} else if (!name.equals(other.name))
		return false;
	return true;
}
```

![enter description here](https://www.github.com/sun6145/githubPicture/raw/master/小书匠/HashSet存储元素保证唯一性的代码及图解.png "HashSet存储元素保证唯一性的代码及图解")

# LinkedHashSet类🌩
 * LinkedHashSet:底层数据结构由哈希表和链表组成。
 * 哈希表保证元素的唯一性。
 * 链表保证元素有素。(存储和取出是一致)
```
LinkedHashSet<String> hs = new LinkedHashSet<String>();

// 创建并添加元素
hs.add("hello");
hs.add("world");
hs.add("java");
hs.add("world");
hs.add("java");

// 遍历
for (String s : hs) {
	System.out.println(s);
}
结果:
	hello
	world
	java
```

# TreeSet类🌩
> 底层数据结构是**红黑树**(自平衡二叉树)    
> 唯一性: 根据返回值是否为0来判断
> 排序:
	1. 自然排序(元素具备比较性)
		- 让元素所属的类实现自然排序接口Comparable
	2. 比较器排序
		- 让集合的构造方法接收一个比较器的子类对象Comparator
> **排序和唯一**: 能够对元素按照某种规则进行排序。   
	- 自然排序	
	- 提供的 Comparator (比较器)进行排序	
	- 真正的比较是依赖于元素的compareTo()方法，而这个方法是定义在 Comparable里面的。
		
## 自然排序 
- 基本数据类型都实现了自然排序接口(Comparable)
> 通过观察TreeSet的add()方法，我们知道最终要看TreeMap的put()方法。

### 基本数据类型

```
// 创建集合对象
// 自然顺序进行排序
TreeSet<Integer> ts = new TreeSet<Integer>();

// 创建元素并添加
// 20,18,23,22,17,24,19,18,24
ts.add(20);
ts.add(18);
ts.add(23);
ts.add(22);
ts.add(17);
ts.add(24);
ts.add(19);
ts.add(18);
ts.add(24);

// 遍历
for (Integer i : ts) {
	System.out.print(i+"\t");
}
结果:
17	18	19	20	22	23	24	
```

![enter description here](https://www.github.com/sun6145/githubPicture/raw/master/小书匠/TreeSet存储元素自然排序和唯一的图解.png "TreeSet存储元素自然排序和唯一的图解")

### 引用数据类型
> 如果一个类的元素要想能够进行自然排序，就必须实现自然排序接口

- A:你没有告诉我们怎么排序
	- 自然排序，按照年龄从小到大排序
- B:元素什么情况算唯一你也没告诉我
	- 成员变量值都相同即为同一个元素

```
public class Student implements Comparable<Student>{
	@Override
	public int compareTo(Student s) {
		// return 0;
		// return 1;
		// return -1;

		// 这里返回什么，其实应该根据我的排序规则来做
		// 按照年龄排序,主要条件
		int num = this.age - s.age;
		// 次要条件
		// 年龄相同的时候，还得去看姓名是否也相同
		// 如果年龄和姓名都相同，才是同一个元素
		int num2 = num == 0 ? this.name.compareTo(s.name) : num;
		return num2;
	}
}


==========================================
//按照姓名的长度排序
@Override
public int compareTo(Student s) {
	// 主要条件 姓名的长度
	int num = this.name.length() - s.name.length();
	// 姓名的长度相同，不代表姓名的内容相同
	int num2 = num == 0 ? this.name.compareTo(s.name) : num;
	// 姓名的长度和内容相同，不代表年龄相同，所以还得继续判断年龄
	int num3 = num2 == 0 ? this.age - s.age : num2;
	return num3;
}


==================================================
// 创建集合对象
TreeSet<Student> ts = new TreeSet<Student>();

// 创建元素
Student s1 = new Student("lin", 27);
Student s2 = new Student("zhang", 29);
Student s3 = new Student("wang", 23);
Student s4 = new Student("lin", 27);
Student s5 = new Student("liu", 22);
Student s6 = new Student("wu", 40);
Student s7 = new Student("feng", 22);
Student s8 = new Student("lin", 29);

// 添加元素
ts.add(s1);
ts.add(s2);
ts.add(s3);
ts.add(s4);
ts.add(s5);
ts.add(s6);
ts.add(s7);
ts.add(s8);

// 遍历
for (Student s : ts) {
	System.out.println(s.getName() + "---" + s.getAge());
}
```

## 比较器排序
### 集合的比较器排序
> TreeSet<Student> ts = new TreeSet<Student>(); //自然排序
> public TreeSet(Comparator comparator(接口)) //比较器排序

```
TreeSet<Student> ts = new TreeSet<Student>(new MyComparator());
```
> 通过匿名内部类
> 如果一个方法的参数是接口，那么真正要的是接口的实现类的对象
> 而匿名内部类就可以实现这个东西

```
TreeSet<Student> ts = new TreeSet<Student>(new Comparator<Student>(){
	@Override
	public int compare(Student s1, Student s2){
		// 姓名长度
		int num = s1.getName().length() - s2.getName().length();
		// 姓名内容
		int num2 = num == 0 ? s1.getName().compareTo(s2.getName())
				: num;
		// 年龄
		int num3 = num2 == 0 ? s1.getAge() - s2.getAge() : num2;
		return num3;
	}
});
```

自己写一个实现了Comparator接口的类
```
public class Mycomparator implements Comparator<Student>{
	@override
	public int compare(Student s1,Stuedent s2){
		int num = s1.getAge()-s2.getAge();
		// 年龄大小
		int num2 = num==0?s1.getName().compareTo(s2.getName()):num;
		// 年龄相同,比较姓名
		return num2;
	}
}
```

# Collection集合总结
	Collection
		|--List	有序,可重复
			|--ArrayList
				底层数据结构是数组，查询快，增删慢。
				线程不安全，效率高
			|--Vector
				底层数据结构是数组，查询快，增删慢。
				线程安全，效率低
			|--LinkedList
				底层数据结构是链表，查询慢，增删快。
				线程不安全，效率高
		|--Set	无序,唯一
			|--HashSet
				底层数据结构是哈希表。
				如何保证元素唯一性的呢?
					依赖两个方法：hashCode()和equals()
					开发中自动生成这两个方法即可
				|--LinkedHashSet
					底层数据结构是链表和哈希表
					由链表保证元素有序
					由哈希表保证元素唯一
			|--TreeSet
				底层数据结构是红黑树。
				如何保证元素排序的呢?
					自然排序
					比较器排序
				如何保证元素唯一性的呢?
					根据比较的返回值是否是0来决定
					
# 针对Collection集合我们到底使用谁呢?(掌握)
	唯一吗?
		是：Set
			排序吗?
				是：TreeSet
				否：HashSet
		如果你知道是Set，但是不知道是哪个Set，就用HashSet。
			
		否：List
			要安全吗?
				是：Vector
				否：ArrayList或者LinkedList
					查询多：ArrayList
					增删多：LinkedList
		如果你知道是List，但是不知道是哪个List，就用ArrayList。
	
	如果你知道是Collection集合，但是不知道使用谁，就用ArrayList。
	
	如果你知道用集合，就用ArrayList。
	
# 在集合中常见的数据结构
	ArrayXxx:底层数据结构是数组，查询快，增删慢
	LinkedXxx:底层数据结构是链表，查询慢，增删快
	HashXxx:底层数据结构是哈希表。依赖两个方法：hashCode()和equals()
	TreeXxx:底层数据结构是二叉树。两种方式排序：自然排序和比较器排序
		
---
title: java基础day14
tags:
  - List
  - Map
date: 2018-08-15 09:26:15
categories: JavaSE
top:
---
# 集合
> 存储可变的数据时,数组不能很好的处理
> 集合是一个容器

二者的区别:
1. 长度
	- 数组长度固定
	- 集合长度不固定
2. 内容不同
	- 数组存储基本类型和引用类型
	- 集合只能存储引用类型
3. 性能上
	- 数组更好
	- 集合底层数据结构复杂
<!-- more -->

## 集合树
1. Collection依赖Iterator
	1. list: 重复,有序,依赖 ListIterator
		1. ArrayList
		2. LinkedList
		3. Vector --> 子类stack
	2. set: 不能重复,map(值,null)
		1. HashSet
			1. LinkedHashSet
		2. Treeset
3. Map依赖collection
	1. HashMap: 存储一对信息
	2. TreeMap
3. comparable 和 comparator 排序器

# Collection接口
> 存储数据,单列数据
> 可以重复,无序

## 常用的方法
1. isEmpty()
	查看集合是否为空
2. size()
	查看集合的长度
3. add()
	往集合中添加元素
3. addAll(集合）
	将集合中的所有元素添加到当前集合中,只要当前集合发生改变true
4. remove()
	将元素添从当前集合中删除,只要当前集合发生改变true
5. removeAll(集合）
	将集合中的所有元素添加到当前集合中,只要当前集合发生改变true
6. removeIf()
	带条件删除,重写Predicate接口的test方法
7. contains()
	查看元素是否在集合中,有一个就是true
8. containsAll(集合)
	判断集合元素是否全部在当前集合中,全部在为true
9. toArray()
	将集合变为数组
10. clear
	清除集合中的所有元素
11. Arrays.asList(String[] str)是Arrays的静态内部类
	数组转集合,不能进行集合长度改变的操作(增加,删除),UnsupportedOperationException
```
//演示collection的功能()
public static void main(String[] args) {
	Collection<String> c = new ArrayList<>();
	System.out.println("isEmpty:" + c.isEmpty());
	// 添加数据
	c.add("aa");
	c.add("bb");
	c.add("cc");

	// 查看集合的长度
	System.out.println("size:" + c.size()); // size:3

	// 遍历
	for (String s : c) {
		System.out.print(s + " "); // aa bb cc
	}

	System.out.println();

	// 添加集合addAll(集合)
	c.addAll(c);
	System.out.println("addAll:" + c);// addAll:[aa, bb, cc, aa, bb, cc]

	// 移除remove(object)
	c.remove("aa");
	System.out.println("remove:" + c);// remove:[bb, cc, aa, bb, cc]

	// 移除集合
	Collection<String> d = new ArrayList<>();
	d.add("aa");
	d.add("bb");
	c.removeAll(d);
	System.out.println("removeAll:" + c); // removeAll:[cc, cc]

	// 带条件删除removeIf
	c.add("abc");
	// c.removeIf(new Predicate<String>() {
	//
	// @Override
	// public boolean test(String t) {
	// return t.length()<=2;
	// }
	// });

	// lambda 表达式
	c.removeIf((t) -> t.length() <= 2);
	System.out.println("removeIf:" + c); // removeIf:[abc]

	// 是否存在参数指定的元素,存在true
	System.out.println("contains:" + c.contains("abc")); // contains:true

	// 是否存在集合参数中的全部元素,全部存在为true
	c.add("aa");
	System.out.println("containsAll:" + c.containsAll(d));// containsAll:false,只有aa包含

	// 数组转换成集合,Arrays.asList是Arrays的静态内部类
	// 不能添加,移除(改变长度)
	List<String> list = Arrays.asList(new String[] { "aa", "bb" });
	// list.add("cc"); //UnsupportedOperationException
	// list.remove("aa"); // UnsupportedOperationException
	System.out.println("get:" + list.get(1)); // get:bb

	// 集合转数组toArray
	Object[] array = d.toArray(new Object[2]);
	// Object[] array = d.toArray();
	String[] array2 = d.toArray(new String[3]);

	// 遍历
	System.out.println("toArray:" + Arrays.toString(array));// toArray:[aa, bb, null]
	
	//移除所有元素
	c.clear();
	System.out.println("clear:" + c);//clear:[]
}

```

# List 接口
1. 存储数据
2. 可以重复
3. 有序,按照元素add的顺序

## 方法
1. add
	添加元素
1. add(index,element)
	向参数索引处添加一个元素
2. get(index)
	获取索引处多的元素
3. set(index,element)
	用第二个参数元素替换第一个参数索引处的元素
4. indexOf(element)
	参数元素第一次在集合中出现的索引位置
5. lastindexOf(element)
	查找 参数元素最后一次在集合中出现的索引位置
6. subList(start,end)
		子集合
7. remove(index)
	删除指定位置的元素
7. sort(Comparator)
	排序Comparator
	使用sort(null):自然 升序 排序

```
public static void main(String[] args) {
	List<String> list = new ArrayList<>();
	// 添加元素
	list.add("aa");
	list.add("bb");
	list.add("cc");
	System.out.println("add:" + list);// add:[aa, bb, cc]

	// 向参数索引处添加一个元素
	list.add(1, "dd");
	System.out.println("Index add:" + list);// Index add:[aa, dd, bb, cc]

	// 用第二个参数元素替换第一个参数索引处的元素
	list.set(1, "hello");
	System.out.println("set:" + list);// set:[aa, hello, bb, cc]

	// 查找 参数元素第一次在集合中出现的索引位置
	System.out.println("indexOf:" + list.indexOf("hello"));// indexOf:1

	// 查找 参数元素最后一次在集合中出现的索引位置
	System.out.println("lastIndexOf:" + list.lastIndexOf("hello"));// lastIndexOf:1
	
	// 子集合
	list.subList(0, 2);
	System.out.println("subList:" + list.subList(0, 2));//subList:[aa, hello]
	// 排序Comparator
	System.out.println("set:" + list);//set:[aa, hello, bb, cc]
	
	//按照 自然 升序 排序
	list.sort(null);
	System.out.println("sort:"+list);//sort:[aa, bb, cc, hello]
	
	//删除
	list.remove(1);
	System.out.println("remove:"+list);//remove:[aa, cc, hello]
}
```

## 集合的遍历
### 基本for 只能是List
```
//1.基本for 只能 List-------------------------
for(int i = 0; i < list.size(); i++) {
	System.out.println(list.get(i));
}
```

### 增强for 集合都能用
```
//2.增强for ---------------------------------
for(String s : list) {
	System.out.println(s);
}
```

### 集合的forEach方法
> `list.forEach`(Consumer接口)
`list.forEach(System.out::println)`:方法的引用

```
list.forEach(new Consumer<String>() {
	//t 集合元素
	@Override
	public void accept(String t) {
		System.out.println(t);
	}
});

list.forEach(t->System.out.println(t));//lambda

list.forEach(System.out::println);//方法的引用
```

### 集合的流遍历
list.stream().forEach(System.out::println);


### 迭代器Iterator
#### 迭代器的方法
1. hasNext()
2. next
3. forEachRemaining(Consumer接口);

> 遍历的时候不能操作,并发修改异常使用ListIterator可以操作

remove(): 删除最近一次next()的元素

```
Iterator<String> i = list.iterator();
while(i.hasnext()){
	System.out.println(i.next());
}
```

#### 迭代器的forEachRemaining
```
Iterator<String> i = list.iterator();
i.forEachRemaining(System.out::println);
```
#### ListIterator
1. void add():将指定的元素插入到列表中（可选操作）。 
2. void remove():从列表中删除最后一个元素是由 next()或 previous()返回（可选操作）。 
3. void set():取代过去的元素返回 next()或 previous()与指定的元素
2. hasNext()
3. next()
3. hasPrevious()
4. previous()

```
//next 遍历后可以向上遍历
ListIterator<String > i = list.listIterator();
while(i.hasPrevious()){
	System.out.println(i.previous());
}

//修改,添加,删除.没有并发异常
while(iterator.hasNext()){
	String next = iterator.next();
	if("aa".equals(next)){
		iterator.set("123");
		iterator.remove();
		iterator.add("456");
	}
}
```


## 实现类
1. ArrayList
	1. 底层数据结构是 数组(查找快)
	2. 按照50% 扩容
	3. 遍历 和随机访问效率高
2. Vector(jdk1.0出现,性能低)
	1. Stack,可以模拟栈的操作
	2. 按照100%扩容
	3. 线程安全
	4. 使用工具类包装arraylist代替掉
3. LinkedList
	1. 底层数据结构 是链表(增删快)
	1. 链表的数据用节点表示:
		1. 构成
			1. 值域数据
			2. 链域地址
		3. 单向链表:值域+ 链域
		4. 双向链表: 前驱 + 值域 + 后继

> 数组和链表的区别

1. 
	- 数组的地址是连续的
	- 链表的地址不连续
2. 
	- 数组:遍历 和随机访问效率高
	- 链表:添加元素和删除元素效率高

# set接口
> 集合特点:无序且唯一

```
//获取7个不重复的随机数
public static void main(String[] args) {
	// Set
	Set<Integer> set = new HashSet<>();
	int r;
	while(true) {
		r = (int)(Math.random()*(30-1+1)+1);
		set.add(r);// int ->Integer
		if(set.size() == 7) {
			break;
		}
	}
	set.forEach(System.out::println);
	
}
```
## HashSet类
> 底层数据结构是哈希表

### hash唯一存储原理
首先,调用 hashCode()算出一个hash地址,把元素存进去,
如果hash值冲突hashCode()的值一样,这时候才会调用equals()判断二个对象是否相同,相同不存储,不同可以存储
需要重写hashCode()和equals方法.

```
@Override
public int hashCode() {
	// 哈希地址 
	return name.hashCode() + no;
}

@Override
public boolean equals(Object obj) {
	// this ,obj
	// obj _ > Employee
	Employee e = (Employee)obj;
	return this.no == e.no && this.name.equals(e.name);
}
```

## LinkedHashSet
> 有序,唯一
> 链表,根据插入的顺序


## SortedSet接口
> 是Set的子接口,
> 实现类TreeSet;
> 底层数据结构是 二叉树

1. first()	获得第一个元素
	. last()	获得最后一个元素
	. subSet(start, end)	得到子集,[起始元素,终止元素)


```
public static void main(String[] args) {
	SortedSet<Integer> set = new TreeSet<>();
	set.add(111);
	set.add(25);
	set.add(153);

	System.out.println(set);// [25, 111, 153]
	// 获得第一个元素
	System.out.println(set.first());// 25

	// 获得最后一个元素
	System.out.println(set.last());// 153

	// 子集,[起始元素,终止元素)
	System.out.println(set.subSet(25, 111));// [25]
}
```

### 数:表示层次结构的
> 树：是由节点集及连接每对节点的有向边集组成。
> 二叉树：树形结构任意节点不能超过两个孩子。
1. 根节点
2. 枝节点
3. 叶节点
4. 中序遍历: 左中右
5. 要管理树(维持持续),效率不如hashSet

## NavigableSet接口
> 是sortedSet的子类接口
> 提供了接近匹配原则的检索元素的方法

1. floor(e):小于 等于 指定参数 的最大元素
2. ceiling:大于等于 指定参宿的 最小元素
3. descendingSet():集合元素降序排序
4. descendingIterator():降序的迭代器
5. pollFirst():移除第一个元素
6. pollLast(): 移除最后一个元素


```
public static void main(String[] args) {
	// NavigableSet
	NavigableSet<Double> set = new TreeSet<>();
	set.add(11.1);
	set.add(55.5);
	set.add(22.2);
	set.add(99.9);
	System.out.println(set);	//[11.1, 22.2, 55.5, 99.9]
	
	//小于 等于 指定参数 的最大元素
	System.out.println(set.floor(20.0));	//11.1
	
	//大于等于 指定参宿的 最小元素
	System.out.println(set.ceiling(20.0));	//22.2
	
	//降序的集合
	set.descendingSet().forEach(System.out::println);
	
	//降序的迭代器
	set.descendingIterator().forEachRemaining(System.out::println);
	
	结果降序:
	/*
		99.9
		55.5
		22.2
		11.1
	*/

	//移除第一个元素
	set.pollFirst();
	System.out.println(set);	//[22.2, 55.5, 99.9]
	
	//移除最后一个元素
	set.pollLast();
	System.out.println(set);	//[22.2, 55.5]
}
```

## TreeSet
> 真正的比较是依赖于元素的compareTo()方法，而这个方法是定义在 Comparable里面的。
所以，你要想重写该方法，就必须是先 Comparable接口。这个接口表示的就是自然排序。
**二叉树不需要重写hashCode()和equals()**

```
compareTo()方法中:
return 0: 不添加到集合
return正数: 作为右孩子
return 负数: 作为左孩子

通过中序遍历可以得到有序
```
# List集合的排序

1. sort(null): 自然排序,需要排序的类中实现Comparable接口
2. sort(comparator): 推荐使用lambda表达式


```
class Dog implements Comparable<Dog>{
	@Override
	public int compareTo(Dog o) {
		return this.age - o.age ;
	}
	
}
```

```
public static void main(String[] args) {
	List<Dog> dogs = new ArrayList<Dog>();
	Dog wangwang = new Dog("旺旺","金毛",2);
	Dog meimei = new Dog("美美","吉娃娃",3);
	Dog wangcai = new Dog("旺财","松狮",1);
	dogs.add(wangwang);
	dogs.add(meimei);
	dogs.add(wangcai);
	
	//遍历
	dogs.forEach(System.out::println);
	
	//2.实现自然排序
	dogs.sort(null);
	
	//实现comparator年龄降序
	dogs.sort((dog1,dog2)->dog2.getAge() - dog1.getAge());
	
	//删除美美
	dogs.remove(meimei);
	
	//
	System.out.println(dogs.size());	//2

}
```


# 使用选择
1. 数据情况
	1. (Set)唯一:
		1. 无序:HashSet
		2. 有序:
			1. 添加顺序: LinkedList
			2. 自定义顺序: TreeSet
	2. (List)重复:
		1. 查询多: ArrayList
		2. 增删多: linkedList
	


# 参考
1. [集合](/2018/07/31/集合/ "集合")
6. [List接口](/2018/07/31/List接口/ "List接口")
7. [List集合练习](/2018/08/02/List集合练习/ "List集合练习")
8. [Set接口](/2018/08/03/Set接口/ "Set接口") 

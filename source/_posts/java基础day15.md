---
title: java基础day15
tags:
  - Queue
  - Map
  - Stream
date: 2018-08-16 09:13:55
categories:
top: 
---

# Collections类
针对集合操作的工具类

<!-- more -->

## 方法：
> 都是静态方法

1. sort(list)
	自然升序排序

2. sort(集合，比较器)
	自定义排序方法

3. binarySearch(list,e)
	查找参数元素e在集合list出现的索引,前提**升序**

4. max(list)
	集合中的最大元素

5. min(list)
	集合中的最小元素

6. reverse(list)
	对集合元素进行反转

7. frequency(list,e)
	查找参数元素e在集合元素中出现的次数,不存在为0

	. fill	(list,e)
	用元素e填充list
	
9. shuffle(list)
	集合元素的洗牌

```
public static void main(String[] args) {
		List<String> list = new ArrayList<>();
		//
		Collections.addAll(list, "aa","cc","bb");
		System.out.println(list);

		//自然 升序
		Collections.sort(list);
		System.out.println(list);

		//指定比较器
//		Collections.sort(list, (s1,s2)->s2.compareTo(s1));
		System.out.println(list);

		//查找参数 元素 在集合中 出现的索引 ， 前提   升序 排序
//		System.out.println(Collections.binarySearch(list, "aaa"));

		//集合 中 最小 的 和 最大 
		System.out.println(Collections.min(list));
		System.out.println(Collections.max(list));
		//
		list.add("aa");
		System.out.println(list);

		//查找 参数 元素 在集合 中 出现的 次数 ，不存在  0
		System.out.println(Collections.frequency(list, "aa"));//2

		//对集合元素进行反转
		Collections.reverse(list);
		System.out.println(list);

		//集合元素的洗牌
		Collections.shuffle(list);
		System.out.println(list);

		//集合的填充 ，用 参数 来替换 集合 中的每个元素。
		Collections.fill(list, "xxx");
		System.out.println(list);
		
	}
```

# Queue接口
> 队列: 是访问受限的线性表

先进先出的数据结构

1. 添加元素从队尾添加
2. 删除元素从队头删除

## 方法
|添加|删除|获取列表头元素|操作失败时|
|:----:|:----:|:----:|:----:|
|add|remove |element |会产生异常|
|offer |poll |peek |不会产生异常，而是返回特定的值。|

## 实现类
### LinkedList类
> 队列不允许添加null,但是linkedList可以添加
1. add(e)
  向集合添加元素:(向后添加)
  	成功： 返回true;
  	失败： 返回异常
2. offer(e)
  向集合添加元素:(向后添加)
  	成功： 返回true;
  	失败： 返回false
3. remove()
  向集合删除元素:(第一个)
  	成功： 返回true;
  	失败： 返回异常
4. poll()
  向集合删除元素:(第一个)
  	成功： 返回true;
  	失败： 返回null
5. element()//不删除查看元素

```
Queue<String> q = new LinkedList<>();
q.add("aa");
q.add("bb");
q.offer("cc");
q.forEach(System.out::println);

//出队---------------------------
System.out.println(q.remove());
System.out.println(q.poll());

//循环出队 -----------------------------------
while(q.size() > 0) {
	System.out.println(q.poll());//移除,查看第一个
	System.out.println(q.peek());//第一个没有走,死循环
}
```

### PriorityQueue类
1. 优先队列
2. 违背了队列先进先出的规则
3. 使用该类不要使用集合的forEach遍历,看不到效果

> 默认构造使用自然升序优先级（comparable）
> 带参(comparator)
> **不能使用list的forEach（）；

```
public static void main(String[] args) {
	// 优先队列
	Queue<Integer> q = new PriorityQueue<>((n1, n2) -> n2 - n1);
	q.add(22);
	q.add(55);
	q.add(11);
	// 没有效果
	// q.forEach(System.out::println);

	// 遍历
	while (q.size() > 0) {
		System.out.println(q.poll());
	}
}

逆序输出:
55
22
11
```

# Deque 接口
1. 双端队列
2. 模拟栈（后进先出）:
	1. addFirst---getFirst();
	2. addLast----getLast()
3. 是Queue的子类

## 方法
|添加|删除|获取列表头元素|操作失败时|
|:----:|:----:|:----:|:----:|
|addFirst |removeFirst |getFirst |会产生异常|
|addLast |removeLast| getLast|会产生异常|
|offerFirst| pollFist |peekFirst |不会产生异常，而是返回特定的值。|
|offerLaset |pollLast |peekLast|不会产生异常，而是返回特定的值。|

栈的方法：
> push
	入栈：添加
> pop
	弹栈：删除

## 实现类ArrayDeque();
### 队列(队尾进,队头出)
```
入队		出队
add()		remove()
offer()		poll()
addLast()	removeFirst()
offerLast()	pollFirst()
```

```
//模拟 队列
	Deque<String> d = new ArrayDeque<>();
	d.add("aa");
	d.addLast("bb");
	d.offer("cc");
	d.offerLast("dd");
	System.out.println(d);
	//出队
	while(d.size() > 0) {
	//	System.out.println(d.poll());
		System.out.println(d.pollFirst());
	}
```
### 栈(从上入栈,从上出栈)
```
入栈		出栈
addFirst()	pollFirst()
push()		pop()
```
```
Deque<String> d = new ArrayDeque<>();
	//入栈
	d.addFirst("aa");
	d.offerFirst("bb");
	d.addFirst("cc");
	d.push("dd");
	//出栈
	while(d.size() > 0) {
	//	System.out.println(d.pollFirst());
		System.out.println(d.pop());
	}
```


# Map接口
双列存储, 键值对
键是唯一的

## 实现类HashMap
> HashMap支持 存储入 null键 和 null值。
> 键是唯一 的，覆盖了之前的重复的


## 方法

1. size()

2. isEmpty()

3. containsKey()

4. containsValue()

5. remove()

6. keySet()

7. values()

8. clear()

```
public static void main(String[] args) {
	// TODO Auto-generated method stub
	Map<Integer,String> map = new HashMap<>();

	//存信息,添加
	map.put(1, "张三");
	map.put(2, "李四");
	map.put(3, "王五");
	System.out.println(map);

	//获得 键值对 的数量
	System.out.println(map.size());//3

	//键值对的数目 为0  true
	System.out.println(map.isEmpty());//false

	//查看 指定 的键  在 集合 中  是否 存在;
	System.out.println(map.containsKey(2));

	//查看 指定 的值在 集合 中  是否 存在;
	System.out.println(map.containsValue("李四"));

	//根据 指定的键去删除  
	map.remove(2);
	System.out.println(map);

	//获得 键的集合 Set
	Set<Integer> set = map.keySet();
	System.out.println(set);

	//值的集合
	Collection<String> c = map.values();
	System.out.println(c);
	//
	//	map.clear();
	//	System.out.println(map.isEmpty());
	//---------------------------------------
	System.out.println(map);
	//HashMap支持 存储入 null键 和 null值。
	map.put(null, null);	//null = null
	System.out.println(map);

	//键是唯一 的，覆盖了之前的重复的
	map.put(1, "赵六");
	System.out.println(map);
}

```

## 遍历
1. foreach
```java
map.forEach(new BiConsumer<Integer, String>() {

	@Override
	public void accept(Integer t, String u) {
		System.out.println(t + "," + u);
	}
});
map.forEach((k,v)-> System.out.println(k+":"+v));
```

2. 键值的迭代器遍历
```
map.keySet().iterator().forEachRemaining(System.out::println);

map.values().iterator().forEachRemaining(System.out::println);

```
3. 键值对的集合entrySet()
```
Set<Entry<Integer,String>> set = map.entrySet();
//输出键值对
set.forEach(e->System.out.println(e));	//键值对对象
set.forEach(e->System.out.println(e.getKey()+","+e.getValue()));	//获取键和值

//键值对的迭代器		
```

4. 键值对的迭代器遍历
```
Set<Entry<Integer,String>> set1 = map.entrySet();
//键值对的迭代器
Iterator<Entry<Integer,String>> i = set1.iterator();
i.forEachRemaining(System.out::println);
		
```

# Stream 流操作
1. `java.util.*` 包下,1.8出现
2. 对流中的数据进行聚集运算。
3. 一次性的运算
4. 速度快

- 使用:
  - IntStream
  - LongStream
  - DoubleStream

## 聚集方法

1. max():获取流中的最大值
2. min()
3. sum()
4. average()
5. count()
6. allMatch()是否所有元素符合条件
7. anyMatch()是否至少包含一个元素
8. filter: 过滤器,**中间方法,返回的是一个新的流,可以继续调用方法**

> 以上的方法使用一次后(比如求玩最大值后,流对象就不能继续使用)
>
> 解决办法:
>
> ​	使用一次后重新创建一次流

##  实现类

### IntStream

#### 末端方法:

​	得到结果后 就释放了
	IntStream  is = IntStream.build();	

​	IntStream  is = IntStream.builder().add(11).build();

```java
public static void main(String[] args) {
    IntStream is = IntStream.builder().add(11).add(22).add(33).add(55).build();
    // 末端方法-------------------------------------------
    // System.out.println(is.max().getAsInt());	//55
    // System.out.println(is.min().getAsInt());	//11
    // System.out.println(is.sum());	//121
    // System.out.println(is.average().getAsDouble());	//30.25
    // System.out.println(is.count());
    
    // 带条件的流统计
    // all 所有 的 数据 都满足条件 返回 true
    // allMatch(): 是否所有元素符合条件
    is.allMatch(new IntPredicate() {
			
			@Override
			public boolean test(int value) {
				return value>20;
			}
		});
    
    System.out.println(is.allMatch(v->v > 10));	//true:流中的所有元素大于10
    
    //any 流中只要有一个元素满足条件 就返回true
    System.out.println(is.anyMatch(v-> v > 50));	//true
```



#### 中间方法:

​	得到一个新的流,可以进行新的操作

```java
IntStream is = IntStream.builder().add(11).add(22).add(33).add(55).build();
 
is.filter(v-> v>20).forEach(System.out::println);//中间方法
```





## 集合使用流

### 集合转换为流

1. stream():将集合的元素转换为流

```java
public static void main(String[] args) {
    List<Integer> list = new ArrayList<>();
    Collections.addAll(list, 11, 454, 44, 6878, 23);
    list.stream().filter(v -> v < 55).forEach(System.out::println);
    System.out.println(list.stream().filter(v -> v < 55).count());
    //
    list.stream().forEach(System.out::println);

}
```





# Predicate 接口

> 过滤器的接口

使用的位置:

1. ;流中的filter ()方法
2. 集合中的removeIf()方法

例子"

> 过滤学生年龄在30岁以上并且名字中包含"g"的学生

```java
// 学生类
class Student {
	private String name;
	private int age;

	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}

	public int getAge() {
		return age;
	}

	public void setAge(int age) {
		this.age = age;
	}

    public Student(String name, int age) {
        super();
        this.name = name;
        this.age = age;
    }

    public Student() {
        super();
        // TODO Auto-generated constructor stub
    }

    @Override
    public String toString() {
        return "Student [name=" + name + ", age=" + age + "]";
    }

}
```

```java
// 测试过滤器
public class TestPredicate1 {

	public void showStu(List<Student> stus, Predicate<Student> p) {
		for (Student stu : stus) {
			if (p.test(stu)) {
				System.out.println(stu);
			}
		}
	}

	public static void main(String[] args) {
		List<Student> stus = new ArrayList<>();
		stus.add(new Student("zhangsan", 44));
		stus.add(new Student("lisi", 55));
		stus.add(new Student("wangwu", 22));
		new TestPredicate1().showStu(stus, t -> t.getAge() > 30 && t.getName().contains("g"));
	}

}
```


---
title: Collections工具类
tags:
  - Collections工具类
  - 模拟斗地主
date: 2018-08-06 14:39:05
categories: Java
top:
---
> Collections:是针对集合进行操作的工具类，都是静态方法。
> Collection:是单列集合的顶层接口，有子接口List和Set。
> Collections:是针对集合操作的工具类，有对集合进行排序和二分查找的方法
> 
<!-- more -->

# Collections成员方法
	* public static <T> void sort(List<T> list)：排序 默认情况下是自然顺序。
 	* public static <T> int binarySearch(List<?> list,T key):二分查找
 	* public static <T> T max(Collection<?> coll):最大值
 	* public static void reverse(List<?> list):反转
	* public static void shuffle(List<?> list):随机置换

代码测试
```
// 创建集合对象
List<Integer> list = new ArrayList<Integer>();

// 添加元素
list.add(30);
list.add(20);
list.add(50);
list.add(10);
list.add(40);

System.out.println("list:" + list);	//list:[30, 20, 50, 10, 40]

Collections.sort(list);
System.out.println("list:" + list);	//list:[10, 20, 30, 40, 50]

System.out.println("binarySearch:" + Collections.binarySearch(list, 30));	//binarySearch:2(前提:有序)
System.out.println("binarySearch:" + Collections.binarySearch(list, 300));	//binarySearch:-6

System.out.println("max:"+Collections.max(list));	//max:50

Collections.reverse(list);
System.out.println("list:" + list);	//list:[40, 10, 50, 20, 30]

Collections.shuffle(list);
System.out.println("list:" + list);	//list:[20, 40, 30, 10, 50]  list:[40, 10, 20, 50, 30]等等
```

# 排序

## 比较器排序
static <T> void sort(List<T> list, Comparator<? super T> c) 
根据指定的比较器指定的顺序对指定的列表进行排序  
如果同时有自然排序和比较器排序，以比较器排序为主
````
Collections.sort(list, new Comparator<Student>() {
@Override
public int compare(Student s1, Student s2) {
	int num = s1.getAge() - s2.getAge();
	int num2 = num == 0 ? s1.getName().compareTo(s2.getName()): num;
	return num2;
}
});
```

## 自然排序
> Student 类要实现 implements Comparable<Student>
> 重写compareTo 方法
```
@Override
public int compareTo(Student s) {
	int num = this.age - s.age;
	int num2 = num == 0 ? this.name.compareTo(s.name) : num;
	return num2;
}
```

# 模拟斗地主
```
public static void main(String[] args) {
//1.创建一个牌盒
ArrayList<String> array = new ArrayList<String>();
//2.装牌
//定义一个花色数组
String[] colors = {"♠", "♥", "♣", "♦"};
//定义一个点数
String[] numbers = {"A", "2", "3", "4", "5", "6", "7", "8", "9", "10",
        "J", "Q", "K"};
//3.装牌
for (String color : colors) {
    for (String number : numbers) {
        array.add(color.concat(number));
    }
}
//4.洗牌
Collections.shuffle(array);

//5.发牌
ArrayList<String> zhangsan = new ArrayList<String>();
ArrayList<String> lisi = new ArrayList<String>();
ArrayList<String> wangwu = new ArrayList<String>();
ArrayList<String> diPai = new ArrayList<String>();
for (int i = 0; i < array.size(); i++) {
    if (i < array.size() - 3) {
        if (i % 3 == 0) {
            zhangsan.add(array.get(i));
        }
        if (i % 3 == 1) {
            lisi.add(array.get(i));
        }
        if (i % 3 == 2) {
            wangwu.add(array.get(i));
        }
    } else {
        diPai.add(array.get(i));
    }
}
//6.看牌
//张三看牌
System.out.print("张三:");
for (String str : zhangsan)
    System.out.print(str + " ");
System.out.print("\n李四:");
for (String str : lisi)
    System.out.print(str + " ");
System.out.print("\n王五:");
for (String str : wangwu)
    System.out.print(str + " ");
System.out.print("\n底牌:");
for (String str : diPai)
    System.out.print(str + " ");
}
```

## 对扑克牌排序(整理)
	思路：
		A:创建一个HashMap集合
		B:创建一个ArrayList集合
		C:创建花色数组和点数数组
		D:从0开始往HashMap里面存储编号，并存储对应的牌
			同时往ArrayList里面存储编号即可。
		E:洗牌(洗的是编号)
		F:发牌(发的也是编号，为了保证编号是排序的，就创建TreeSet集合接收)
		G:看牌(遍历TreeSet集合，获取编号，到HashMap集合找对应的牌)
```
public static void main(String[] args) {
// 创建一个HashMap集合
HashMap<Integer, String> hm = new HashMap<Integer, String>();

// 创建一个ArrayList集合
ArrayList<Integer> array = new ArrayList<Integer>();

// 创建花色数组和点数数组
// 定义一个花色数组
String[] colors = { "♠", "♥", "♣", "♦" };
// 定义一个点数数组
String[] numbers = { "3", "4", "5", "6", "7", "8", "9", "10", "J", "Q",
		"K", "A", "2", };

// 从0开始往HashMap里面存储编号，并存储对应的牌,同时往ArrayList里面存储编号即可。
int index = 0;

for (String number : numbers) {
	for (String color : colors) {
		String poker = color.concat(number);
		hm.put(index, poker);
		array.add(index);
		index++;
	}
}
hm.put(index, "小王");
array.add(index);
index++;
hm.put(index, "大王");
array.add(index);

// 洗牌(洗的是编号)
Collections.shuffle(array);

// 发牌(发的也是编号，为了保证编号是排序的，就创建TreeSet集合接收)
TreeSet<Integer> fengQingYang = new TreeSet<Integer>();
TreeSet<Integer> linQingXia = new TreeSet<Integer>();
TreeSet<Integer> liuYi = new TreeSet<Integer>();
TreeSet<Integer> diPai = new TreeSet<Integer>();

for (int x = 0; x < array.size(); x++) {
	if (x >= array.size() - 3) {
		diPai.add(array.get(x));
	} else if (x % 3 == 0) {
		fengQingYang.add(array.get(x));
	} else if (x % 3 == 1) {
		linQingXia.add(array.get(x));
	} else if (x % 3 == 2) {
		liuYi.add(array.get(x));
	}
}

// 看牌(遍历TreeSet集合，获取编号，到HashMap集合找对应的牌)
lookPoker("张三", fengQingYang, hm);
lookPoker("李四", linQingXia, hm);
lookPoker("王五", liuYi, hm);
lookPoker("底牌", diPai, hm);
}

// 写看牌的功能
public static void lookPoker(String name, TreeSet<Integer> ts,
	HashMap<Integer, String> hm) {
System.out.print(name + "的牌是：");
for (Integer key : ts) {
	String value = hm.get(key);
	System.out.print(value + " ");
}
System.out.println();
}

张三的牌是：♠A ♦A ♣3 ♠4 ♦4 ♥5 ♦6 ♠7 ♥7 ♣8 ♥9 ♣9 ♥J ♦J ♥Q ♠K 小王 
李四的牌是：♥A ♣A ♥2 ♦2 ♥3 ♦3 ♥4 ♣4 ♣5 ♦5 ♣7 ♦7 ♥8 ♦9 ♥10 ♦10 ♣Q 
王五的牌是：♠2 ♣2 ♠3 ♠6 ♥6 ♣6 ♠8 ♦8 ♠9 ♣10 ♠J ♠Q ♦Q ♥K ♣K ♦K 大王 
底牌的牌是：♠5 ♠10 ♣J 
```
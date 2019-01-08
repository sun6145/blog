---
title: Map子类
tags:
  - HashMap
  - null
date: 2018-08-06 16:44:42
categories: Java
top:
---

# HashMap类概述
键是哈希表结构，可以保证键的唯一性

<!-- more -->

## HashMap<String,String>
```
// 创建集合对象
HashMap<String, String> hm = new HashMap<String, String>();

// 创建元素并添加元素
// String key1 = "001";
// String value1 = "欧展鹏";

hm.put("001", "欧展鹏");
hm.put("002", "嘉澜");
hm.put("002", "嘉澜");
hm.put("003", "丘黎");
hm.put("004", "如空月");
hm.put("005", "言思晴");
hm.put("001", "东方不败");

// 遍历
Set<String> set = hm.keySet();
for (String key : set) {
	String value = hm.get(key);
	System.out.println(key + "---" + value);
}
结果:
	001---欧展鹏
	002---嘉澜
	003---丘黎
	004---如空月
	005---东方不败
```

## HashMap<Integer,String>
```
// 创建集合对象
HashMap<Integer, String> hm = new HashMap<Integer, String>();

// 创建元素并添加元素
// Integer i = new Integer(27);
// Integer i = 27;
// String s = "欧展鹏";
// hm.put(i, s);

hm.put(27, "欧展鹏");
hm.put(30, "丘黎");
hm.put(28, "如空月");
hm.put(29, "嘉澜");

// 下面的写法是八进制，但是不能出现8以上的单个数据
// hm.put(003, "hello");
// hm.put(006, "hello");
// hm.put(007, "hello");
// hm.put(008, "hello");

// 遍历
Set<Integer> set = hm.keySet();
for (Integer key : set) {
	String value = hm.get(key);
	System.out.println(key + "---" + value);
}

// 下面这种方式仅仅是集合的元素的字符串表示
// System.out.println("hm:" + hm);
}
结果: 
	27---欧展鹏
	28---如空月
	29---嘉澜
	30---丘黎
```

## HashMap<String,Student>
```
// 创建集合对象
HashMap<String, Student> hm = new HashMap<String, Student>();

// 创建学生对象
Student s1 = new Student("欧展鹏", 58);
Student s2 = new Student("如空月", 55);
Student s3 = new Student("迦南", 54);
Student s4 = new Student("球溪", 50);

// 添加元素
hm.put("9527", s1);
hm.put("9522", s2);
hm.put("9524", s3);
hm.put("9529", s4);

// 遍历
Set<String> set = hm.keySet();
for (String key : set) {
	// 注意了：这次值不是字符串了
	// String value = hm.get(key);
	Student value = hm.get(key);
	System.out.println(key + "---" + value.getName() + "---"
			+ value.getAge());
}
结果: 
	9524---迦南---54
	9522---如空月---55
	9529---球溪---50
	9527---欧展鹏---58
```

## HashMap<Student,String>
```
// 创建集合对象
HashMap<Student, String> hm = new HashMap<Student, String>();

// 创建学生对象
Student s1 = new Student("貂蝉", 27);
Student s2 = new Student("王昭君", 30);
Student s3 = new Student("西施", 33);
Student s4 = new Student("杨玉环", 35);
Student s5 = new Student("貂蝉", 27);

// 添加元素
hm.put(s1, "8888");
hm.put(s2, "6666");
hm.put(s3, "5555");
hm.put(s4, "7777");
hm.put(s5, "9999");

// 遍历
Set<Student> set = hm.keySet();
for (Student key : set) {
	String value = hm.get(key);
	System.out.println(key.getName() + "---" + key.getAge() + "---"
			+ value);
}

//要求：如果两个对象的成员变量值都相同，则为同一个对象。
//哈希表作用是用来保证键的唯一性的。
//需要重写hashCode()和equals()方法

结果:
	王昭君---30---6666
	貂蝉---27---9999
	杨玉环---35---7777
	西施---33---5555
```

# LinkedHashMap类
Map 接口的哈希表和链接列表实现，具有可预知的迭代顺序。
* 由哈希表保证键的唯一性
* 由链表保证键盘的有序(存储和取出的顺序一致)
```
// 创建集合对象
LinkedHashMap<String, String> hm = new LinkedHashMap<String, String>();

// 创建并添加元素
hm.put("2345", "hello");
hm.put("3456", "java");
hm.put("1234", "world");
hm.put("1234", "javaee");
hm.put("3456", "android");

// 遍历
Set<String> set = hm.keySet();
for (String key : set) {
	String value = hm.get(key);
	System.out.println(key + "---" + value);
}
```

# TreeMap类概述
键是红黑树结构，可以保证键的排序和唯一性

## HashMap<String,String>
```
// 创建集合对象
TreeMap<String, String> tm = new TreeMap<String, String>();

// 创建元素并添加元素
tm.put("hello", "你好");
tm.put("world", "世界");
tm.put("java", "爪哇");
tm.put("world", "世界2");
tm.put("javaee", "爪哇EE");

// 遍历集合
Set<String> set = tm.keySet();
for (String key : set) {
	String value = tm.get(key);
	System.out.println(key + "---" + value);
}

结果:
	hello---你好
	java---爪哇
	javaee---爪哇EE
	world---世界2
```

## TreeMap<Student,String>
```
// 创建集合对象
TreeMap<Student, String> tm = new TreeMap<Student, String>(
		new Comparator<Student>() {
			@Override
			public int compare(Student s1, Student s2) {
				// 主要条件
				int num = s1.getAge() - s2.getAge();
				// 次要条件
				int num2 = num == 0 ? s1.getName().compareTo(
						s2.getName()) : num;
				return num2;
			}
		});

// 创建学生对象
Student s1 = new Student("潘安", 30);
Student s2 = new Student("柳下惠", 35);
Student s3 = new Student("唐伯虎", 33);
Student s4 = new Student("燕青", 32);
Student s5 = new Student("唐伯虎", 33);

// 存储元素
tm.put(s1, "宋朝");
tm.put(s2, "元朝");
tm.put(s3, "明朝");
tm.put(s4, "清朝");
tm.put(s5, "汉朝");

// 遍历
Set<Student> set = tm.keySet();
for (Student key : set) {
	String value = tm.get(key);
	System.out.println(key.getName() + "---" + key.getAge() + "---"
			+ value);
}
结果:
	潘安---30---宋朝
	燕青---32---清朝
	唐伯虎---33---汉朝
	柳下惠---35---元朝
```

# 

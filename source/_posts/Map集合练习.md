---
title: Map集合练习
tags:
  - 集合的嵌套遍历
date: 2018-08-06 17:21:44
categories: Java
top:
---
****

<!-- more -->

1. 获取字符串中每一个字母出现的次数
> 分析：
		A:定义一个字符串(可以改进为键盘录入)
		B:定义一个TreeMap集合
			键:Character
			值：Integer
		C:把字符串转换为字符数组
		D:遍历字符数组，得到每一个字符
		E:拿刚才得到的字符作为键到集合中去找值，看返回值
			是null:说明该键不存在，就把该字符作为键，1作为值存储
			不是null:说明该键存在，就把值加1，然后重写存储该键和值
		F:定义字符串缓冲区变量
		G:遍历集合，得到键和值，进行按照要求拼接
		H:把字符串缓冲区转换为字符串输出
		录入：aababcabcdabcde
		结果：result:a(5)b(4)c(3)d(2)e(1)
```
// 定义一个字符串(可以改进为键盘录入)
Scanner sc = new Scanner(System.in);
System.out.println("请输入一个字符串：");
String line = sc.nextLine();

// 定义一个TreeMap集合
TreeMap<Character, Integer> tm = new TreeMap<Character, Integer>();

//把字符串转换为字符数组
char[] chs = line.toCharArray();

//遍历字符数组，得到每一个字符
for(char ch : chs){
	//拿刚才得到的字符作为键到集合中去找值，看返回值
	Integer i =  tm.get(ch);
	
	//是null:说明该键不存在，就把该字符作为键，1作为值存储
	if(i == null){
		tm.put(ch, 1);
	}else {
		//不是null:说明该键存在，就把值加1，然后重写存储该键和值
		i++;
		tm.put(ch,i);
	}
}

//定义字符串缓冲区变量
StringBuilder sb=  new StringBuilder();

//遍历集合，得到键和值，进行按照要求拼接
Set<Character> set = tm.keySet();
for(Character key : set){
	Integer value = tm.get(key);
	sb.append(key).append("(").append(value).append(")");
}

//把字符串缓冲区转换为字符串输出
String result = sb.toString();
System.out.println("result:"+result);
```
# 集合的嵌套遍历
## HashMap嵌套HashMap
```
// 创建集合对象
HashMap<String, HashMap<String, Integer>> map1 = new HashMap<String, HashMap<String, Integer>>();
HashMap<String, Integer> map2 = new HashMap<String, Integer>();

// 添加元素
map2.put("元素1", 20);
map2.put("元素2", 22);
// 把小集合添加到大集合
map1.put("大集合1", map2);

HashMap<String, Integer> map3 = new HashMap<String, Integer>();
map3.put("元素1", 21);
map3.put("元素2", 23);
map1.put("大集合2", map3);

//遍历
//遍历集合
Set<String> mapSet = map1.keySet();
for(String mapKey : mapSet){
	System.out.println(mapKey);
	HashMap<String, Integer> mapValue = map1.get(mapKey);
	Set<String> mapValueSet = mapValue.keySet();
	for(String mapValueKey : mapValueSet){
		Integer mapValueValue = mapValue.get(mapValueKey);
		System.out.println("\t"+mapValueKey+"---"+mapValueValue);
	}
}

大集合1
	元素1---20
	元素2---22
大集合2
	元素1---21
	元素2---23
```

## HashMap嵌套ArrayList
```
// 创建集合对象
HashMap<String, ArrayList<String>> hm = new HashMap<String, ArrayList<String>>();

// 创建元素集合1
ArrayList<String> array1 = new ArrayList<String>();
array1.add("吕布");
array1.add("周瑜");
hm.put("三国演义", array1);

// 创建元素集合2
ArrayList<String> array2 = new ArrayList<String>();
array2.add("令狐冲");
array2.add("林平之");
hm.put("笑傲江湖", array2);

// 创建元素集合3
ArrayList<String> array3 = new ArrayList<String>();
array3.add("郭靖");
array3.add("杨过");
hm.put("神雕侠侣", array3);

//遍历集合
Set<String> set = hm.keySet();
for(String key : set){
	System.out.println(key);
	ArrayList<String> value = hm.get(key);
	for(String s : value){
		System.out.println("\t"+s);
	}
}

神雕侠侣
	郭靖
	杨过
三国演义
	吕布
	周瑜
笑傲江湖
	令狐冲
	林平之
```

## ArrayList嵌套HashMap
```
public static void main(String[] args) {
// 创建集合对象
ArrayList<HashMap<String, String>> array = new ArrayList<HashMap<String, String>>();

// 创建元素1
HashMap<String, String> hm1 = new HashMap<String, String>();
hm1.put("周瑜", "小乔");
hm1.put("吕布", "貂蝉");
// 把元素添加到array里面
array.add(hm1);

// 创建元素1
HashMap<String, String> hm2 = new HashMap<String, String>();
hm2.put("郭靖", "黄蓉");
hm2.put("杨过", "小龙女");
// 把元素添加到array里面
array.add(hm2);

// 创建元素1
HashMap<String, String> hm3 = new HashMap<String, String>();
hm3.put("令狐冲", "任盈盈");
hm3.put("林平之", "岳灵珊");
// 把元素添加到array里面
array.add(hm3);

// 遍历
for (HashMap<String, String> hm : array) {
	Set<String> set = hm.keySet();
	for (String key : set) {
		String value = hm.get(key);
		System.out.println(key + "---" + value);
	}
}

吕布---貂蝉
周瑜---小乔
杨过---小龙女
郭靖---黄蓉
令狐冲---任盈盈
林平之---岳灵珊

```

## HashMap嵌套HashMap嵌套ArrayList(三层嵌套)
```
// 创建大集合
HashMap<String, HashMap<String, ArrayList<Student>>> map = new HashMap<String, HashMap<String, ArrayList<Student>>>();
// 北京数据
HashMap<String, ArrayList<Student>> bjMap = new HashMap<String, ArrayList<Student>>();
ArrayList<Student> array1 = new ArrayList<Student>();
Student s1 = new Student("学生1", 27);
Student s2 = new Student("学生2", 30);
array1.add(s1);
array1.add(s2);

ArrayList<Student> array2 = new ArrayList<Student>();
Student s3 = new Student("学生3", 28);
Student s4 = new Student("学生4", 29);
array2.add(s3);
array2.add(s4);

bjMap.put("基础班", array1);
bjMap.put("就业班", array2);
map.put("北京校区", bjMap);

// 西安校区数据
HashMap<String, ArrayList<Student>> xaMap = new HashMap<String, ArrayList<Student>>();
ArrayList<Student> array3 = new ArrayList<Student>();
Student s5 = new Student("学生5", 27);
Student s6 = new Student("学生6", 30);
array3.add(s5);
array3.add(s6);
ArrayList<Student> array4 = new ArrayList<Student>();
Student s7 = new Student("学生7", 28);
Student s8 = new Student("学生8", 29);
array4.add(s7);
array4.add(s8);
xaMap.put("基础班", array3);
xaMap.put("就业班", array4);
map.put("西安校区", xaMap);

//集合遍历
Set<String> mapSet = map.keySet();
for (String mapKey : mapSet) {
	System.out.println(mapKey);
	HashMap<String, ArrayList<Student>> mapValue = map
			.get(mapKey);
	Set<String> mapValueSet = mapValue.keySet();
	for (String mapValueKey : mapValueSet) {
		System.out.println("\t" + mapValueKey);
		ArrayList<Student> czbkMapValueValue = mapValue
				.get(mapValueKey);
		for (Student s : czbkMapValueValue) {
			System.out.println("\t\t" + s.getName() + "---"
					+ s.getAge());
		}
	}
}

西安校区
	就业班
		学生7---28
		学生8---29
	基础班
		学生5---27
		学生6---30
北京校区
	就业班
		学生3---28
		学生4---29
	基础班
		学生1---27
		学生2---30

```
# Hashtable和HashMap的区别?
Hashtable:线程安全，效率低。不允许null键和null值
HashMap:线程不安全，效率高。允许null键和null值

# 2:List,Set,Map等接口是否都继承子Map接口?
List，Set不是继承自Map接口，它们继承自Collection接口
Map接口本身就是一个顶层接口
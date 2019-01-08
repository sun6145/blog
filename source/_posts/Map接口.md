---
title: Map接口
tags:
  - Map集合
date: 2018-08-06 14:28:30
categories: Java
top:
---
# Map接口概述
	将键映射到值的对象
		一个映射不能包含重复的键
		每个键最多只能映射到一个值
	Map接口和Collection接口的不同
		Map是双列的,Collection是单列的
		Map的键唯一,Collection的子体系Set是唯一的
		Map集合的数据结构值针对键有效，跟值无关
	    Collection集合的数据结构是针对元素有效

> Map集合的一个最大的特点，就是它可以存储键值对的元素。

<!-- more -->

## Map接口成员方法'
	1:添加功能
			V put(K key,V value):添加元素。
 			如果键是第一次存储，就直接存储元素，返回null
			如果键不是第一次存在，就用值把以前的值替换掉，返回以前的值
	2:删除功能
		void clear():移除所有的键值对元素
 		V remove(Object key)：根据键删除键值对元素，并把值返回
	3:判断功能
		boolean containsKey(Object key)：判断集合是否包含指定的键
		boolean containsValue(Object value):判断集合是否包含指定的值
		boolean isEmpty()：判断集合是否为空
	4:获取功能
		Set<Map.Entry<K,V>> entrySet():返回的是键值对对象的集合
		V get(Object key):根据键获取值
 		Set<K> keySet():获取集合中所有键的集合
 		Collection<V> values():获取集合中所有值的集合
	5：长度功能
		int size()：返回集合中的键值对的对数
```
// 创建集合对象
Map<String, String> map = new HashMap<String, String>();

//添加元素
System.out.println("put:" + map.put("0001", "麓殇"));		//put:null
System.out.println("put:" + map.put("0001", "旧青画"));		//put:麓殇
System.out.println("put:" + map.put("0002", "蓝奕世"));		// put:null
System.out.println("put:" + map.put("0003", "不可言"));		// put:null
System.out.println("put:" + map.put("0004", "苏莫晨"));		// put:null

//void clear():移除所有的键值对元素
map.clear();
System.out.println("size:"+map.size());		//size:0

System.out.println("remove:" + map.remove("0001"));	//remove:旧青画
System.out.println("remove:" + map.remove("0005"));	//remove:null
System.out.println("map:" + map);		//map:{0004=苏莫晨, 0002=蓝奕世, 0003=不可言}

System.out.println("isEmpty:"+map.isEmpty());		//isEmpty:false

System.out.println("containsKey:" + map.containsKey("0004"));	//containsKey:true
System.out.println("containsKey:" + map.containsKey("0005"));	//containsKey:false
```

```
// 获取功能
// V get(Object key):根据键获取值
System.out.println("get:" + map.get("0001"));	//旧青画
System.out.println("get:" + map.get("周杰")); 	// 返回null

// Set<K> keySet():获取集合中所有键的集合
Set<String> set = map.keySet();
for (String key : set) {
	System.out.println(key);
}
结果:
	0004
	0002
	0003
	0001

// Collection<V> values():获取集合中所有值的集合
Collection<String> con = map.values();
for (String value : con) {
	System.out.println(value);
}
结果:
	苏莫晨
	蓝奕世
	不可言
	旧青画
```

## Map集合的遍历
	思路：
		A:把所有的丈夫给集中起来。
		B:遍历丈夫的集合，获取得到每一个丈夫。
		C:让丈夫去找自己的妻子。
	转换：
		A:获取所有的键
		B:遍历键的集合，获取得到每一个键
		C:根据键去找值
```
// 创建集合对象
		Map<String, String> map = new HashMap<String, String>();

		// 创建元素并添加到集合
		map.put("杨过", "小龙女");
		map.put("郭靖", "黄蓉");
		map.put("杨康", "穆念慈");
		map.put("陈玄风", "梅超风");

		// 遍历
		// 获取所有的键
		Set<String> set = map.keySet();
		// 遍历键的集合，获取得到每一个键
		for (String key : set) {
			// 根据键去找值
			String value = map.get(key);
			System.out.println(key + "---" + value);
		}
	}
```

	思路：
		A:获取所有结婚证的集合
		B:遍历结婚证的集合，得到每一个结婚证
		C:根据结婚证获取丈夫和妻子
	
	转换：
 		A:获取所有键值对对象的集合
		B:遍历键值对对象的集合，得到每一个键值对对象
		C:根据键值对对象获取键和值
```
// 创建集合对象
Map<String, String> map = new HashMap<String, String>();

// 创建元素并添加到集合
map.put("杨过", "小龙女");
map.put("郭靖", "黄蓉");
map.put("杨康", "穆念慈");
map.put("陈玄风", "梅超风");

// 获取所有键值对对象的集合
Set<Map.Entry<String, String>> set = map.entrySet();
// 遍历键值对对象的集合，得到每一个键值对对象
for (Map.Entry<String, String> me : set) {
	// 根据键值对对象获取键和值
	String key = me.getKey();
	String value = me.getValue();
	System.out.println(key + "---" + value);
}
```
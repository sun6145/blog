---
title: List集合练习
tags:
  - ArrayList去除重复
  - LinkedList模拟栈
  - 自定义对象的比较
date: 2018-08-02 9:31:42 
categories: Java
top: 
---
# ArrayList题目
1. 去除集合中字符串的重复值(字符串的内容相同)

2. 去除集合中自定义对象的重复值(对象的成员变量值都相同)

<!-- more-->

----------
# 参考答案：
1. ArrayList去除重复
	```
	分析：
	* A:创建集合对象
	* B:添加多个字符串元素(包含内容相同的)
	* C:创建新集合
	* D:遍历旧集合,获取得到每一个元素
	* E:拿这个元素到新集合去找，看有没有
	*	    有：不搭理它
	*     没有：就添加到新集合
	* F:遍历新集合

		// 创建集合对象
		ArrayList array = new ArrayList();

		// 添加多个字符串元素(包含内容相同的)
		array.add("hello");
		array.add("world");
		array.add("java");
		array.add("world");
		array.add("java");
		array.add("world");
		array.add("world");
		array.add("world");
		array.add("world");
		array.add("java");
		array.add("world");

		// 创建新集合
		ArrayList newArray = new ArrayList();

		// 遍历旧集合,获取得到每一个元素
		Iterator it = array.iterator();
		while (it.hasNext()) {
		String s = (String) it.next();

		// 拿这个元素到新集合去找，看有没有
		if (!newArray.contains(s)) {
			newArray.add(s);
			}
		}

		// 遍历新集合
		for (int x = 0; x < newArray.size(); x++) {
			String s = (String) newArray.get(x);
			System.out.println(s);
		}
	
	```
2. 不创建新的集合去除重复
	```
	// 由选择排序思想引入，我们就可以通过这种思想做这个题目
	// 拿0索引的依次和后面的比较，有就把后的干掉
	// 同理，拿1索引...

	for(int i =0;i<array.size()-1;i++){
		for(int j =i+1;j<array.size();j++){
			if(array.get(i).equals(array.get(j))){
				array.remove(j);
				j--;
			}
		}
	}	
	```

3. 去除集合中自定义对象的重复值
```
Iterator it = array.iterator();
while (it.hasNext()) {
	Student s = (Student) it.next();

	// 拿这个元素到新集合去找，看有没有
	if (!newArray.contains(s)) {
		newArray.add(s);
	}
}

结果：
	对象全部复制到新的数组了；
--------------------------------
原因:
    contains()方法的底层依赖的是equals()方法。
    而我们的学生类中没有equals()方法，这个时候，默认使用的是它父亲Object的equals()方法
    Object()的equals()默认比较的是地址值，所以，它们进去了。因为new的东西，地址值都不同。
    按照我们自己的需求，比较成员变量的值，重写equals()即可。
-------------------------------------------------------------------------------------
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
# linkedList
## LinkedList模拟栈数据结构的集合

栈的特点先进后出

# 参考答案：
LinkedList的特有添加功能addFirst()
```
//创建集合对象
LinkedList link = new LinkedList();

//添加元素
link.addFirst("hello");
link.addFirst("world");
link.addFirst("java");

//遍历
Iterator it = link.iterator();
while(it.hasNext()){
	String s = (String)it.next();
	System.out.println(s);
}
```
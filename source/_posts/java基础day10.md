---
title: java基础day10
tags:
  - null
  - null
date: 2018-08-09 09:00:58
categories: JavaSE
top: 
---
****

<!-- more -->
# UML建模语言
## 类图

访问修饰符
- \- private
- 不写 default
- \# protected
- \+ bublic


# 类和类的关系
## 泛化
> 继承   
> 实线 + 空心三角    
> 子类 指向 --> 父类    

## 实现 
> 接口   
> 接口 成员   
> 虚线 + 空心三角
> 实现 指向 ---> 接口

## 依赖
> 是一种 使用 的 关系
> 虚线 + 箭头
- 人 使用 车
	- 人 指向 车
- 动物 依赖 食物
	- 动物 指向 食物

语法: 成员变量


## 关联
> 拥有
- 学员 拥有 课程
- 实线 + 箭头

对象拥有的个数分类
- 1对1(汽车 和 车位)
- 1对多(学员 和 多门课程)
- 多对多(老师 和 学员)

### 聚集(聚合)(强关联)
 - 整体 和 局部
 - 局部 离开 整体 ,整体可以独立 存在(键盘和鼠标)
 - 空心菱形 + 箭头
 - 菱形执向 整体

### 组合(聚集的一种)
> 实心菱形 + 箭头
> 实心菱形 指向 整体
> - 整体和局部
> 局部离开整体 整体不可以独立存在(人 和 心脏)


语法: 成员变量

# API
## String 
> String 就是一个对象
> String 是不可变类
	- 对象一旦创建就不能更改
```
String s1 = "abc";
String s2 = new String("abc");
String s3 = new String("ABC");
String s4 = "abc";
String s5 = new String("abc");

System.out.println(s1 == s4); // true
System.out.println(s1 == s2); // false
System.out.println(s2 == s5); // false
System.out.println(s2.equals(s5)); // true 
System.out.println(s2.equals(s3)); // false,equals区分大小写

s1:创建了一次对象;将"abc"存入方法区的常量池.
s2:创建了一次对象,第一次new ,在常量池中找到了"abc",将地址给了堆内存,然后堆内存地址给引用.
s3:创建了二次对象,第一次new 在堆,第二次在常量池中创建对象,将地址给了堆内存,然后堆内存地址给引用.

s1="hello"+"tom";
s1.concat("abcde");//连接字符串
System.out.println(s1);//hellotom

//如果想修改s1,就要改引用,s1=新的对象.
s1=s1.concat("abcde");
System.out.println(s1);//hellotomabcde
```

### 成员方法
1. concat(str):字符串连接字符串str.
2. length();判断字符串的长度
2. equals();比较二个字符串是否相等,区分大小写
	- equalsIgnorceCase(): 不区分大小写
3. toUpperCase: 转换为大写
4. toLowerCase: 转换为小写
5. indexOf(String str): 查找 参数 字符串 在原始 字符串 中 第一次 出现的位置索引
	- 没有找到: 返回 -1
6. lastIndexOf(String str):查找 参数 字符串 在原始 字符串 中 最后一次 出现的位置索引
	- 没有找到: 返回 -1
7. charAt(index): 获得指定索引的字符
8. subString(int start):获取从索引开始截取字符串
	- subString(int Start ,int end):截取[start,end)范围的字符串
9. trim(String str): 去掉字符串的前后空格
10. replace(old,new): 使用new 替换 old
	- 替换字符串中间的空格
	- "" 空串 常量池中 有空间,没有字符序列
	- null 空,空对象 没有空间
11. startsWith():判断是否指定的参数开头
12. endsWith():判断是否指定的参数结尾
13. comparreTo(参数对象):与参数对象比较大小
	- 相等:0
	- 小:负数,比较的在参考的ASCII前 (根据厂商的不同,值不同)
	- 小:正数,比较的在参考的ASCII前 (根据厂商的不同,值不同)
14. toCharArray(): 将字符串转换为字符串数组
15. split(" "):用参数/(空格)字符串风格为一个字符串数组
16. contains(String):指定的参数的字符串 在原字符串中是否存在,不存在为false 	

## StringBuffer
> 可变的字符串格式,成倍扩充容量(影响性能)
> 构造方法可以自定义容量.
> 默认给16个字符大小的缓冲区
```
StringBuffer sb = new StringBuffer("hello")// 缓冲区:16+5
自己定义:
StringBuffer sb = new StringBuffre(100);
```

### 成员方法
1. capacity():查看缓冲区的大小
2. append():追加,返回一个StringBuffer对象
	- 可以添加任意类型
	- append (char[] ,start , end): 从start位置 插入end个字符
3. insert(index , str):指定位置插入一个字符串
4. setCharAt(index,char):修改指定位置的字符
5. deleteCharAt(index):删除指定位置的字符
6. delete(start,end):删除索引区间为[start , end)的字符
7. reverse(): 反转
8. trimToSize()
9. charAt()
10. indexof()
11. lastIndexOf()

## String,StringBuffer,StringBuilder的区别
String:不可变类,表示一个字符串的使用,简单
	- 如果字符串的值 频繁更改,用缓冲的

多线程使用 : StringBuffer,线程是安全的
StringBuilder 线程不安全
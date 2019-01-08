---
title: java增强
tags:
  - null
  - null
date: 2018-09-25 8:50:12
categories:
top:
---


集合

# list

## arrayList

底层结构:数组(长度不可变)+实现了序列化接口

实现

- 创建数组的时候  长度是0

- 第一次添加元素的时候   初始化数组的长度   10

  ```
  数组的扩容：
      数组的元素个数超过10个的时候   扩容的工作
      grow（）
      int newCapacity = oldCapacity + (oldCapacity >> 1);
      =1.5*oldCapacity
      数组扩容的时候 每次1.5倍进行扩容的
      进行数组的复制的工作	
  ```

```java
数组的上限：arrayList数组的上限：Integer_max(21亿)-8
15亿------15*1.5=====22.亿
数组的上限   integer_maxvalue  21亿
arraylist的size的最大上限是integer_maxvalue
	性能瓶颈10亿左右


右移1位   /2    2位  /4
左移：1   *2     2  *4


构造方法中有参构造   无参构造
public ArrayList(int initialCapacity) {
    if (initialCapacity > 0) {
    	this.elementData = new Object[initialCapacity];
    } else if (initialCapacity == 0) {
		this.elementData = EMPTY_ELEMENTDATA;
    } else {
    	throw new IllegalArgumentException("Illegal Capacity: "+
   		 initialCapacity);
    }

}	
```
```
有参构造的使用场景：
如果使用无参构造，初始化数组的长度0  第一次添加元素的时候开始  长度10   以后的每次扩容   1.5倍进行扩容
1000
10   15    22   33    50   75   112   。。。。
数组扩容----数组的拷贝的过程  消耗资源的过程

使用有参构造  参数：代表的是我们数组初始化的长度
参数500---750----1000+
当arraylist中元素个数很多的时候  最好使用有参构造   减少底层数组的扩容的次数   提升性能
add（E e）  
remove(E e)
特点：
查询快   增删慢
数组有索引的   通过下标直接访问  时间复杂度o(1)
时间复杂度o(1)  数组    hash
```

## linkedlist

底层是一个链表结构的,有序(元素的插入顺序),可重复

链表

 1. 单向链表

    ```
    只有一个方向的链表   上一个元素知道下一个元素  但是下一个元素不知道上一个元素的  访问的时候只能从一端开始
    ```

 2. 双向链表

    ```
    两个方向的链表   每一个元素  都知道自己的上一个元素和下一个元素是谁
    可以从两个方向访问	
    ```

	3. 环形链表:首位相连的链表



> LinkedList的底层的结构  链表--线性的（1.5--->单向的   1.5之后---> 双向的）
> 链表结构中的每一个元素  就叫做node  对象   Node

```java
//Node的结构：
private static class Node<E> {
    E item;	//本身元素
    Node<E> next; 	//下一个元素
    Node<E> prev;	//上一个元素

    Node(Node<E> prev, E element, Node<E> next) {
        this.item = element;
        this.next = next;
        this.prev = prev;
    }
}
addFirst(E e)
```

添加元素的时候 实际上是对Node的操作

优点：**增删快   查询慢**



## vector：
vector 和arrayList的区别	
​	vector：线程安全的  但是性能低
​	arrayList  线程不安全的   性能高

## stack：

栈结构:先进后出的



# 比较器：

## 内部比较器

```
comparable
	.....ble   使。。。。。具备。。。。的能力
    comparable   使。。。。具备比较的能力
    如果一个类实现了这个接口  代表这个类具备了比较的能力
			内部比较器  定义在对象类定义的时候   只能用于比较本类对象的
			
实现了comparable接口的类需要重写compareTo方法
参数o用于比较的对象 和本对象比较(this)
返回值: int类型
	this.属性-o.属性的结果
	>0 :向后蹿,越大越在后面(升序)
	=0 :位置不变
	<0 :向前蹿(降序)
		
```

## 外部比较器
	专门定义一个比较器的类   这个类中定义比较规则
	comparator
	定义在类的外部的  可以随着传入的泛型的类型  定义比较规则的
	对list集合中的元素进行排序：
# Set

## HashSet

1. 先对key(元素)取hash值

   1. hash冲突 hash碰撞

   2. hash算法:就是生成一个唯一的散列的数   

      > 不存在一个绝对完美的hash算法,任意二个或多个key的hash值不同
      >
      > 任何一个hash算法都会存在hash冲突的问题
      >
      > hash冲突:不同的key的hash值一样

2. 对于同一个hash值,通过链表存储(查询性能低)

   > 1.8后对链表进行了优化,将链表转换为数结构,元素超过8个(阀值)后会转换为数
   >
   >

3. 去重

   1. hash值不同的二个元素, 一定不是同一个元素
   2. 先判断hash值 equals(地址) 不存

## TreeSet

红黑数

排序: **存的数据必须要是可比较的,否则会报错**

​	默认:数值:自然排序

​		字符串:字典排序 1;11;12;2;21;33;4



# map

## HashMap

底层结构:数组+桶表(单向的链表)+树(红黑树)

1. 源码:

   1. 初始容量

      > static final int DEFAULT_INITIAL_CAPACITY = 1 << 4; // aka 16

   2. 最大容量

      >  static final int MAXIMUM_CAPACITY = 1 << 30;

   3. 加载因子:数组扩容的阀值

      数组的容量(下标)达到数组的容量的0.75就会进行扩容

      每层扩容 扩大2倍 16--32

      扩容2倍的原因,这样原来的数据的hash值只能分为当前位置和新的位置,移动教少

      >  static final float DEFAULT_LOAD_FACTOR = 0.75f;

   4. 链表转为数结构的阀值(最大值)

      >   static final int TREEIFY_THRESHOLD = 8;

   5. 树结构转为链表结构的阀值

      数组扩容的原因:原来的桶表的数据被分担了二份,对于数据量少的没有必要转为树结构

      > static final int UNTREEIFY_THRESHOLD = 6;

   6. static final int MIN_TREEIFY_CAPACITY = 64;

## hashTable 和hashMap

hashTable:线程安全 性能低 整个数组的线程锁

hashmap:线程非安全 性能高

## ConcurrentHashMap

> 既可以做到线程安全 又可以保证性能
>
> 分段线程锁+读写锁

锁的分类:

​	读锁:共享锁 所有的线程可以共用这把锁

​	写锁: 排它锁 同一时间只能允许一个线程操作

会对hashmap底层的数组进行分段加锁,通过具体的操作 决定加的是读锁还是写锁





# 排序

## 递归

> 方法自己调用自己

递归原则:

   	1. 必须写一个独立的方法
      ​      	2. 递归必须要有出口(不能是死循环)
         	3. 递归--- 有规律

```java
package java增强;

public class Jiecheng {
    public static void main(String[] args) {
        // System.out.println(jc(5));
        System.out.println(bsst(1));
        System.out.println(bsst(2));
        System.out.println(bsst(3));
        System.out.println(bsst(4));
        System.out.println(bsst(5));
        System.out.println(bsst(6));
    }

    // 阶乘
    public static int jc(int n) {
        if (n == 1) {
            return 1;
        } else {
            return n * jc(n - 1);
        }
    }


    // 不死神兔
    public static int bsst(int i) {
        if (i == 1 || i == 2) {
            return 1;
        } else {
            return bsst(i - 1) + bsst(i - 2);
        }
    }
}

```



## 快速

1. 选定基准点(一般选数组的第一个元素)

   > 作用划分数据
   >
   > ​	基准点左侧: 比基准点小的数据
   >
   > ​	基准点右侧: 比基准点大的数据

2. 通过循环遍历

3. 

## 归并

## 计数排序


---
title: java基础day05
tags:
  - 数组操作
  - Arrays类
  - 类和对象
date: 2018-08-02 09:28:15
categories: JavaSE
top:  
---

# 数组的常用操作

<!-- more -->

## 最大值
```
int[] arr = { 15, 21, 36, 100, 89, 78 };
int max = arr[0];
for (int i = 1; i < arr.length; i++) {
	if (arr[i] > max) {
		max = arr[i];
	}
}
System.out.println("最大值：" + max);
```

## 最小值
```
int[] arr = { 15, 21, 36, 100, 89, 78 };
int min = arr[0];
for (int i = 1; i < arr.length; i++) {
	if (arr[i] < max) {
		min = arr[i];
	}
}
System.out.println("最大值：" + min);
```

## 数组的遍历
### for循环遍历
```
int[] arr = { 15, 21, 36, 100, 89, 78 };
for (int i = 0; i < arr.length - 1; i++) {
		System.out.println(arr[i]);
	}
}
```

### 增强for遍历
```
int[] arr = { 15, 21, 36, 100, 89, 78 };
for( int a : arr){
	System.out.print(a+" ");
}

```
- 增强for :更加简洁，一个一个向后访问，，不用考虑越界。
- 遍历所有元素
- 只能顺序遍历
- 不能改变元素的值(对象的属性可以改)

## 数组排序
> 升序：从小到大
> 降序：从大到小

### 冒泡排序
> 效率并不高
> 冒泡排序是将数组中的两个元素进行比较，并将最小的元素交换到前面
> 排序次数： arr.length-1;
```
int[] arr = { 15, 21, 36, 100, 89, 78 };
for (int i = 0; i < arr.length - 1; i++) {
	for (int j = 0; j < arr.length - 1 - i; j++) {
		if (arr[j] > arr[j + 1]) {
			int temp = arr[j];
			arr[j] = arr[j + 1];
			arr[j + 1] = temp;
		}
	}
}
System.out.println(Arrays.toString(arr));
```

### 选择排序
```
private static void sort(int[] arr) {
	for(int i =0;i<arr.length-1;i++){
		for(int j =i+1;j<arr.length;j++){
			if(arr[i]>arr[j]){
				int temp=arr[i];
				arr[i]=arr[j];
				arr[j]=temp;
			}
		}
	}
	for(int a : arr) {
		System.out.print(a+" ");
	}
}
```

### 插入排序
> 检查数组列表中的每个元素，并将其放入已排序元素中的适当位置
> 当最后一个元素放入合适位置时，该数组排序完毕

```
public static void main(String[] args) {
	int[] arr = new int[] { 12, 56, 32, 76, 8, 45, 61 };
	Demo1.sort(arr);
}

private static void sort(int[] arr) {
	for(int i = 1;i<arr.length;i++){
		for(int j=0;j<i;j++){
			if(arr[i]<arr[j]){
				int temp=arr[i];
				for(int k=i;k>j;k--){
					arr[k]=arr[k-1];
				}
				arr[j]=temp;
			}
		}
	}
	System.out.println(Arrays.toString(arr));
}
```

## 数组查找

### 普通遍历查找

### 二分查找
```
Scanner superman = new Scanner(System.in);
int start = 0;
int end = arr.length-1;

//中间位置
int middle = (start + end) / 2;

System.out.println("--输入要找的数字：");
int n = superman.nextInt();

//中间位置数字不等于 查找的数就循环
while(arr[middle] != n) {
	if(n > arr[middle]) {
		start = middle +1;
	}else if(n < arr[middle]) {
		end = middle -1;
	}
	if(start > end) {
		middle = -1;
		System.out.println("不存在");
		break;
	}
	middle = (start + end) / 2;	
}
System.out.println(middle);

```

## 数组的复制

### 创建新数组接收

### 更改数组的引用

### System类
> **`System.arraycopy(src, srcPos, dest, destPos, length);`**
> - src -源阵列。 
- srcPos -源数组中的起始位置。 
- dest -目标数组。 
- destPos在目标数据的起始位置。 
- length -数组元素的数目被复制。 

### Object类
> **`protected Object clone() 创建并返回此对象的副本。  `**


### Arrays类
常用方法：
- toString（array);	数组变字符串 
- parallelSort(array):在多核的情况下排序	
- 升序排序sort(array);		
- sort(array ,start,end)；对范围[start,end)的数据排序	
- binarySearch(arr ,searchValue);二分查找（前提：排好序的数组）	，
	- <b>找不到:返回插入的（位置+1）的负数</b>	
- equals();判断数组元素是否相等，个数和位置都要相同
- fill()：数据填充
- fill(arr,起始位置，结束位置，填充值):对指定范围内的数据填充
- copyof(arr,元素个数)：产生新的数组对象；
- copyofRange(arr,from ,to):复制[fron，to)范围元素，返回数组对象

```
int[] arr = { 15, 21, 36, 100, 89, 78 };
//方式一
int[] newArr = new int[6];
for (int i = 0; i < arr.length; i++) {
	newArr[i] = arr[i];
}
System.out.println("newArr:" + Arrays.toString(newArr));

// 方式二
int[] arr2 = new int[arr.length];
System.arraycopy(arr, 0, arr2, 0, arr.length);
System.out.println("arr2:" + Arrays.toString(arr2));

// 方式三
int[] arr3;
arr3 = arr;
System.out.println("arr3:" + Arrays.toString(arr3));

// 方式四
int[] arr4 = arr.clone();
System.out.println("arr4:" + Arrays.toString(arr4));

// 方式五
int[] arr5 = Arrays.copyOf(arr, arr.length);
System.out.println("arr5:" + Arrays.toString(arr5));
```

# 二维数组
int[][] arr = new arr[行][列]
> 本质是一个一元数组里放一个一元数组

```
Scanner sc = new Scanner(System.in);
int[][] arr = new int[2][3];
//赋值
for (int i = 0; i < 2; i++) {
	for (int j = 0; j < 3; j++) {
		System.out.print("请输入：");
		arr[i][j] = sc.nextInt();
	}
}

// 遍历
for (int i = 0; i < 2; i++) {
	for (int j = 0; j < 3; j++) {
		System.out.print(arr[i][j]+ " ");
	}
	System.out.println();
}
```

## 不规则二维数组
格式：
```
- int[][] arr2 = new int[][]{{1},{1,2},{1,2,3}};

- int[][] arr = new int[row][];

for(int i=0;i<row;i++){
	arr[i]=new int[i+1];
}
//不初始化会报空指针异常
```

### 杨辉三角形
```
for(int i=0;i<row;i++){
	arr[i]=new int[i+1];
	for(int j=0;j<arr[i].length;j++){
		if(j==0||j==arr[i].length-1){
			arr[i][j]=1;
		}else{
			arr[i][j]=arr[i-1][j-1]+arr[i-1][j];
		}
		
		System.out.print(arr[i][j]+"\t");
	}
	System.out.println();
}
```

# 类和对象

## 对象
> 实体： 一切可以被描述的事物

## 类
> 相似对象的集合，抽象的

```
访问修饰符
public	 class 类名{
	//定义属性部分
	属性1的类型属性1；
	属性2的类型属性2；
	..
	属性n的类型属性n；
	
	//定义方法部分
	方法1；
	方法2；
	...
	方法m；
}
```
- 类名
	- 类名：帕斯卡命名法
	- 多个单词组成，每个单词首字母大写。
	- Demo MyDemo

## 类和对象的区别：

1. 类抽象的；对象是具体的；
2. 类是一个模板，根据这个模板创建出来对象：
3. 类是用数据类型，引用数据类型；
	String 类   	“abc”
	Person 类 	张三
	对象是引用变量。

## 类中的成员
> 成员变量：

- 在类中直接定义的。
- 系统自动初始化。
	- 初始值：
	- 整数：0
	- 浮点：0.0
	- 布尔：false
	- 字符：’\u0000’
	- 字符串：nul1I
	- 引用类型：nul1（空类型）


> 局部变量

- 在方法或代码块中声明的变量
- 自己初始化，系统不会自动初始化



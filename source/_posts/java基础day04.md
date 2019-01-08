---
title: java基础day04
tags:
  - 非固定次数循环
  - 小数格式化
  - 一维数组
  - 队、栈内存
date: 2018-08-01 09:27:22
categories: JavaSE
top:  
---
# 非固定次数循环
- do -while
- for循环

<!-- more -->

## do - while
先执行，后条件判断
```
do{

}while();
```

**while 和 do - while的区别**	
	- while:
		- 先判断，后执行	
		- 首次判断为假，一次都不执行	
	- do - while:	
		- 先执行，后判断	
		- 首次判断为为假，也执行一遍	

## for	
格式：
	```
	for(表达式一；表达式二；表达式三){	
		循环体	
	}	
	表达式二去掉 --- 死循环	
	分号不可以去掉
	```

## 小数的格式化
- java.lang	&emsp;&emsp;基础类库
- java.util	&emsp;&emsp;工具类，集合接口
- java.math	&emsp;&emsp;数学类
- java.text &emsp;&emsp;处理文本、日期、数字和消息的类和接口(格式化）

### printf语句
```
//显示小数%f，整数%d，字符%c，布尔%b，
double sum = 3.1415927;
System.out.printf("结果是：%f\n", sum);			//结果是：3.141593
System.out.printf("结果是：%.2f\n", sum);		//结果是：3.14
```

### DecimalFormat 类
> 使用0，不足用0补；#代表位数
```
DecimalFormat df=new DecimalFormat("0000.00");
System.out.println(df.format(46.65657));	//0046.66
DecimalFormat df1=new DecimalFormat("####.##");
System.out.println(df1.format(45.654657));	//45.65
DecimalFormat df1=new DecimalFormat(".00");
System.out.println(df1.format(45.6));	//45.60
```

## 跳转语句

### break
	作用：结束循环
	场合：	循环，switch
```
// 九九乘法表
public static void main(String[] args) {
	for(int i=1,j =1;i<=9;i++){//j行 i列
		System.out.print(i+"*"+j+"="+(i*j)+"\t");
		if(i==j){
			if(j==9)
				break;
			j++;
			i=0;
			System.out.println();
		}
	}
}
```

### continue 
	- 作用： 结束本次循环，后面的不执行，进入下一次循环
	- 用在循环
```
//输出1-10之间的偶数
for(int i =0; i<= 10; i++){
	if（i%2 != 0)
		continue;
	System.out.print(i+" ");
}
```

### break结束外层循环
> 可以在循环的前面加个**`标签:`**
 
```
public static void main(String[] args) {

	a: for (int i = 1; i <= 5; i++) {// 行
		for (int j = 1; j <= 5; j++) {// 列
			if (j == 3) {
				// break; // 结束内循环
				break a;// 结束外循环
				//continue;		//第三列消失
			}
			System.out.print(i + "行" + j + "列\t");
		}
		System.out.println();
	}
	System.out.println("外循环后的代码。");
}

break a;结果：
1行1列	1行2列	外循环后的代码。

continue a;结果：
1行1列	1行2列	2行1列	2行2列	3行1列	3行2列	4行1列	4行2列	5行1列	5行2列	外循环后的代码。
-------------------------------------
break 标签；和return不同
	break可以执行后面的代码

```

# 数组
> 数组是长度固定的容器，存储**相同数据类型**的一组数据

栈内存：模拟的是**后进先出**的数据结构
	入栈：存储数据,只能从栈顶入
	弹栈：取出数据,只能从栈顶出
- 栈的内存相对其他区 容量小
- 存储 基本数据类型数据 和 引用
- 频繁创建 和销毁的数据

堆内存
- 栈的内存大
- 存储对象的数据
- 不会频繁创建和销毁

## 数组的初始化

### 动态初始化
我们规定长度，系统赋默认值。   
格式： **`数据类型[] 数组名 = new 数据类型[数组长度]`**;
初始化值：
- 整数 &emsp;&emsp;0
- 小数&emsp;&emsp;0.0
- char&emsp;&emsp;'\u0000'
- boolean&emsp;&emsp;false
- String(引用类型)&emsp;&emsp;null

### 手动初始化
元素个数就是数组的长度。
格式： 
- **`数据类型[] 数组名 = new 数据类型[]{元素，元素，（元素，.....）}`**;
简写：
- **`数据类型[] 数组名 = {元素，元素，（元素，.....）}`**;
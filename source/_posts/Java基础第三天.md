---
title: Java基础第三天
tags:
  - 运算符优先级
  - 控制台输入
  - 流程控制语句
date: 2018-07-31 09:08:28
categories: JavaSE
top: 
---

# 运算符的优先级
> 口诀：单目乘除为关系，逻辑三目后赋值。 

<!-- more -->

单目：单目运算符+ –(负数) ++ -- 等 
乘除：算数单目运算符* / % + - 
为：位移单目运算符<< >> 
关系：关系单目运算符> < >= <= == != 
逻辑：逻辑单目运算符&& || & | ^ 
三目：三目单目运算符A > B ? X : Y 
后：无意义，仅仅为了凑字数 
赋值：赋值=

![运算符优先级](https://www.github.com/sun6145/githubPicture/raw/master/小书匠/priority.png "运算符优先级")

# 控制台输入
1. 导包
2. 创建对象
3. 对象调用方法

```
import java.util.Scanner;
Scanner sc = new Scanner(System.in);
System.out.println("请输入：");
String str =sc.next();
int num1 = sc.nextInt();
double num2 = sc.nextDouble();
```

# 流程控制语句

## 条件语句
> 表达式的结果是一个布尔值，如果是true，直接进入if的方法体中，如果结果为false，则跳过if的方法体，继续执行。

### 单分支if
> 格式 ：&emsp;if（表达式）{方法体}  
注意：

- 方法体的默认语句只有一条的时候，可以省略{ }


### 双分支if - else
> 需要对条件成立和不成立的情况分别处理
格式 ：&emsp;if（表达式）{方法体} else {方法体}
注意：

- 方法体的默认语句只有一条的时候，可以省略{ }
- 
### 多重if选择结构
> 格式 ：&emsp;if（表达式）{方法体} else if（表达式）{方法体}...else{方法体}

注意：

- else{}可以省略，表示条件都不成立的情况
- 当条件成立的时候，后面else if 代码不执行

**判断字符串类型的值是否相等**
- **equals(String str)**：&emsp;*英文时*，*区分大小写*
	```
	if（"张三".equals(str)）{
		System.out.print("姓名是："+"张三");
	}
	```
- == **判断内存地址**
 
## 分支语句

### switch
> 
```
//匹配 就是全等。

语法：
   switch(表达式){
     case 值1:
       表达式的值和 值1匹配上了，需要执行的代码;
       break;
     case 值2:
       表达式的值和 值2匹配上了，需要执行的代码;
     break;
     case 值3:
       表达式的值和 值3匹配上了，需要执行的代码;
     break;
     default:
       如果表达式的值和以上的case后面的值都没有匹配上，那么就执行这里的代码。
       break;
   }

* //不写break会穿透到下一个break
* //default的位置可以任意，没有break，照样穿透执行。
```
- swtich()变量类型**只能是int、short、char、byte和enum类型,String(jdk1.7**）。     
- 当进行case判断时，JVM会自动从上到小扫描，寻找匹配的case，
- 标签不能相同
- 可以使用标签堆叠
- **只能进行等值的判断**
- default标签可以省略

### 嵌套if选择结构
```
if(条件) {
	if(条件) {
		代码块一；
	}else {
		代码块二；
	}
}else {
	代码块三；
}
```

### 多重if 和switch 的区别
1. switch只能等值判断
2. 对于等值问题的判断，switch效率高
3. if能够进行范围的判断

## 循环语句
> 解决重复的问题

### 固定次数
#### while
结题方式：
	1. 初始值
	2. 终止值
	3. 迭代次数
```
int i=0;
while(i<100){
	System.out.println("第"+ (++i) +"遍好好学习"+"");
}
```


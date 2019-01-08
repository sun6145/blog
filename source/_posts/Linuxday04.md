---
title: Linux--shell
tags:
  - null
  - null
date: 2018-09-18 09:29:17
categories:
top:
---


# 初识shell

## 什么是shell

- shell程序:接口,提供了用户和内核交互桥梁。

- shell语言: 命令语言,编程语言。解释性语言(编译型语言) 。shell脚本。

- shell解释器:  sh(unix默认)  bash(linux默认)



<!-- more -->

## shell基本格式

```shell
#!/bin/bash   
#设置默认的shell解释器,如果指定使用某个解释器执行，则此行不生效
echo aa
# echo bb
```

## shell脚本执行

### 指定解释器执行

sh 脚本名称

bash 脚本名称

### 内部命令

`source `或 `.` 脚本名称

功能：

`.`:使Shell**读入指定的Shell程序文件**并依次执行文件中的所有语句(脚本调用另外脚本)

`source`:用于重新执行刚修改的初始化文件,使之立即生效,而不必注销并重新登录

### 通过路径执行(有执行的权限)

>  修改权限 chmod 755 a.sh

>  ./a.sh: 当前路径下的a.sh(相对路径)
>
> 绝对路径: /root/a.sh(/root目录下的a.sh)





# shell注释

- 单行注释:shell中以`#`号开头的代码就是注释
- 多行注释:`:<<!  多行注释   !`



# shell基本语法

## 变量

> 系统的内置变量: 查看 :`set` ;取出:`echo $USER`,`echo $PATH`

### 自定义变量

> `变量名=值`: 

规范:

1. `=`号附近不能用空格

2. 变量名推荐使用大写字母

3. 变量的值有空格的时候(字符串)

   1. 单引号: 内部不能取值,(会脱意)
   2. 双引号: 内部可以取值

   ```shell
   A='this is zs'
   echo $A     # this is zs
   B="this is ls"
   echo $B     # this is ls
   ----------------------------
   C=ww
   echo "this is $C"	# this is ww
   echo 'this is $C'	# this is $C
   ```

4. 获取变量值可以使用${变量名}

5. 只读变量:readonly 变量名 = 变量值(不可删除和修改)

6. 删除变量 :unset 变量名

### 案例

```shell
a.sh
#!/bin/bash
A="A in a.sh"
echo $A
/root/b.sh	  # 创建子进程读取b.sh
. /root/b.sh  # 使用内部命令执行,b.sh内容读到付进程
echo $B

b.sh
#!/bin/bash
B="B in b.sh"
echo $B
echo $A


调用a.sh
A in a.sh
B in b.sh
A in a.sh
B in b.sh

调用b.sh
B in b.sh
```

**总结:**

**1. a.sh 直接调用b.sh ,会让b.sh 在a.sh 所在的子空间中执行**

**2. 子进程空间只能访问父进程中用export定义的变量**

**3. 一个shell进程无法将自己定义的变量提升到父进程空间中去**

**4. source 或 "."执行脚本的时候会让脚本在调用者所在的shell进程的空间中执行** 

### 反引号赋值

> 先将反引号内的结果运算完毕，然后赋值,也可以说$()

> 将当前的系统时间赋值给B
>
> - B=\`date`
> - B=\`$(date)`
> - echo $B
> - 输出家目录下的文件:B=\`ll ~/`  echo -e "$B"





## 变量的有用技巧

> ${var:-aa}:没有值时输出aa
>
> ${var:+aa}:有值是输出aa
>
> ${var:=aa}:没有值时赋值为aa
>
> ${var:?aa}:检测变量是否有值,var为空或者被删除(unset),message会送到标准输出,
>
> 出现在shell脚本中,脚本会停止运行

## 特殊变量

> 脚本执行的时候可以传递参数,参数用空格隔开,尽量使用${}
>
> $?:上一次命令执行的状态码(正确 0   否则返回正数),**还可以接收返回值**
>
> $$:查看进程编号
>
> $0:查看脚本名称
>
> $n:查看脚本参数
>
> $#:参数个数
>
> $*:所有的参数
>
> $@: 所有的参数

```
#!/bin/bash
echo $?
echo $$
echo $0
echo $1
echo $2
echo $#
echo $*
echo $@

bash c.sh cs1 cs2
```

### $*,$@的区别

> echo的每一句输出默认会换行
>
> $* 和 $@ 都表示传递给函数或脚本的所有参数
>
> 没有双引号" ": 二者都以`$1 $2 ... $n `的形式组成参数列表
>
> 有引号的时候:
>
> ​	 $*将所有的参数作为一个整体 ,以`"$1 $2  ... $n"`的形式组成**一个整串**
>
> ​	$@将所有的参数分开,以`"$1" "$2" ... "$n"`的形式组成一个**参数列表**

```shell
#!/bin/bash
for A in $*
do
 echo $A
done
echo "======================"
for A in $@
do
 echo $A
done
echo "======================"
for A in "$*"
do
 echo $A
done
echo "======================"
for A in "$@"
do
 echo $A
done
echo "======================"


运行:sh a.sh aa bb cc
aa
bb
cc
======================
aa
bb
cc
======================
aa bb cc
======================
aa
bb
cc
======================
```





# 运算符

## 算术运算符: 

### `expr` 运算:**二边要加空格**,只能计算整数

1. `+`:加  

  > A=\`expr 2 + 3` 	#5

2. `-`:减

3. `\*`:乘号需要转义

   > A=\`expr 2 \* 3`		# 6

4. `/`:没有小数点

   > A=\`expr 3 / 2`

5. `()`---> \`expr \\\( 2 + 3 \\) \* 4`:可以不写$符号

   ```
    A=`expr \( 2 + 3 \) \* 4` 
    echo $A
    
    A=$((2+3)): 不用管空格
    
    A=$[2+3]: 不用管空格
    
    let A=2: 
    
    let D=A+C	:省略$,不可以使用空格
    
    let D=A++
   ```

6. `()`---> `$((A+B))`:可以不写$符号

7. `()`---> `$[A+B]`:

8. `()`---> `let C=A+B`:



### bc命令:计算小数

```
echo "2.3+3.5"	#2.3+3.5
echo "2.3+3.5" | bc	#5.8

浮点数运算: echo "2.3+3.5" | bc
比较运算: echo "1<2" | bc   (true 为1 ,false 为0)
设置小数点位数: echo "scale=2;10/3" | bc	(3.33 保留二位小数)
设置进制: echo "ibase=2;111" | bc	(将二进制111转为10进制  7)
进制转换: echo "ibase=10;obase=8;10" | bc	(将10进制的10转为8进制  12)
高级运算: echo "3^3" | bc	(计算3的3次幂)
		echo "sqrt(4)" | bc (4的开方)
```

### 三元运算符

> [ condition ] && 真 || 假
>
> [ ]两侧必须有空格

```
[root@hdp01 ~]# [ $(echo "3>2" | bc) -gt 1 ] && echo yes || echo no  
no
```



## 关系运算符

| 运算符 | 等同运算符 | 说明                            |
| ------ | ---------- | ------------------------------- |
| -eq    | =          | 检测二个数相等 ,相等返回true    |
| -nq    | !=         | 检测二个数不相等,不相等返回true |
| -ge    | \>=        | 检测左边大于等于右边,是true     |
| -gt    | >          | 检测左边大于右边,是true         |
| -le    | <=         | 检测左边小于等于右边,是true     |
| -lt    | <          | 检测左边小于右边,是true         |

## 布尔云算法

| 运算符 | 等同运算符 | 说明                       |
| ------ | ---------- | -------------------------- |
| -a     | &&         | 与运算,二者为true,返回true |
| -o     | \|\|       | 或运算                     |
| !      | !          |                            |

## 字符串运算符

| 运算符 | 说明                                  |
| ------ | ------------------------------------- |
| =      | 检测二个字符串是否相等,相等返回true   |
| !=     | 检测二个字符串是否相等,不相等返回true |
| -z     | 检测字符串长度是否为0 , 为0返回true   |
| -n     | 检测字符串长度是否为0, 不为0 返回true |
| str    | 检测字符 串是否为空,不为空返回true    |

## 文件运算符

| 运算符 | 说明                                            |
| ------ | ----------------------------------------------- |
| -d     | 检测是否是目录,是true                           |
| -f     | 检测是否为普通文件,不是目录和设备文件,是true    |
| -e     | 检测是否存在,是true                             |
| -s     | 检测文件是否为空,(文件大小是否大于0),不为空true |
| -r     | 是否可读,是true                                 |
| -w     | 是否可写,是true                                 |
| -x     | 是否可执行,是true                               |
| -b     | 是否为块设备文件,是true                         |
| -c     | 是否是字符设备文件                              |



# 流程控制语句

## if

> read -p 相当于Scanner,读取控制台的数据
>
> if [ condition ]是一句
>
> the 语句1是一句
>
> ;和回车相当于是结束符

```
if [condition];
 then
 	语句1;
elif[ condition ];
 then 
 	语句2;
 	...
else
	语句3;
fi

```

```shell
#!/bin/bash
read -p "请输入成绩:" SCORE
if [ $SCORE -gt 80 ]
 then echo "优秀"
elif [ $SCORE -gt 60 ]
 then echo "良好" 
else echo "一般般"
fi
```



## case

```
case $1 in
值1)
	语句1;
	;;
值2)
	语句二;
	;;
*)
	语句三
esac
```

```shell
#!/bin/bash
read -p "请输入成绩:" SCORE
SCORE=`expr $SCORE / 10 `
case $SCORE in     
10)
 echo "优秀"
 ;;
9)
 echo "优秀"
 ;;
8) 
 echo "优秀"
 ;;
7)
 echo "良好"
 ;;
6)
 echo "一般"
 ;;
*)
 echo "不及格"
esac
```



## while

```shell
while [expression]/((expression))
do
	command
	...
done

#--------------------

i=1
while((i<=3))
do 
  echo $i
  let i++
done

#--------------------

#!/bin/bash
i=1
while [ $i -le 3 ]
do 
  echo $i
  let i++
  i=$((i+1))
done


--result:----------
1
2 
3
```

```shell
#!/bin/bash
i=1
result=0
while ((i<=100))
do
  let result=result+i
  let i++

done
echo $result

------------------------
#!/bin/bash
i=1
result=0
while [ $i -le 100 ]
do
  let result=result+i
  let i++

done
echo $result
```



## for

> 列表是一组值(数字,字符串)组成的序列,每个值通过空格分隔,每循环一次,就将列表中的下一个值赋给变量

- 第一种:

```
for 变量 in 列表
do 	
	command
	......
done


for N in 1 2 3;do echo $N ;done
```

- 第二种:

```
for N in {1..3};do echo $N ; done
```

- 第三种

```
for((i=0;i<=2;i++));do echo "welcome $i times"; done
```

> 例子 



```shell
//求一百的和
#!/bin/bash
result=0
for((i=1;i<=100;i++))
do 
	let result=result+i
done
echo $result
	
 
```

## until

> expression 一般为条件表达式,如果返回false 则继续执行循环体,否则跳出循环
>
> **为true是跳出循环**,和while相反

```shell
until expression((条件))/[]
do 
	command
	......
done
```

```shell
#!/bin/bash
i=1;
sum=0;
until ((i>100))
do
let sum=sum+i
 let i++
done
echo $sum
```

# 九九乘法表

```shell
#!/bin/bash
for((i=1;i<=9;i++))
 do
     for((j=1;j<=i;j++))
        do
        echo -ne "$j * $i = `expr $i \* $j`\t"
       # echo -ne "$j*$i=$[i*j]\t"
        done
     echo
 done
```



# 数组

> 使用小括号 ,数组内的元素用空格隔开

```shell
1. 数组的声明
	arr=(aa bb cc)
2.获取某一个元素
	echo ${arr[0]}
3.添加元素
	arr[3]=dd
4.输出全部
	echo ${arr[*]}
	echo ${arr[@]}
5.获取数组的长度
	echo ${#arr[*]}
6.获取数组的下标
	echo ${!arr[*]}
7. 数组的连接(+=),下标接之间的最大值开始算
	 arr+=(dd ee ff)    
     echo ${arr[*]} 	#aa bb cc dd ee ff
8.删除数组
	删除数组元素,会保留数组对应位置的下标unset ${arr[index]}
	unset arr[0]
9.数组的分片
	${arr[@]:number1:number2} 起始位置,取多少

```

```shell
arr=(aa bb cc)
arr[10]=5
echo ${arr[*]}
# aa bb cc 5
echo ${#arr[*]}
# 4
echo ${!arr[*]}
# 0 1 2 10

没有给值得位置没有值
```

## 数组的遍历

> 数组可能不是连续的,不能使用for(;;)的形式遍历

```shell
1. for元素循环遍历
for a in ${arr[*]};do echo $a ;done

2. 遍历下标
for a in ${!arr[*]};do echo ${arr[$a]} ;done
for a in ${!arr[*]};do echo ${arr[a]} ;done
```

# 函数

> 一段代码块
>
> 声明: 函数名(){command...}
>
> 调用:函数名

```sh
-- fun.sh
#!/bin/bash
hello(){
 echo "aa"
}

# 调用函数
hello

----------------
sh fun.sh


```

## 带参数和返回值

```shell
-- fun.sh
#!/bin/bash
hello(){
 echo $1
 echo $2
}


# 调用函数
hello aa bb

# 接收返回值
echo $?


--------------------------------
aa
bb
10
```



## 跨脚本调用函数

```shell
--base.sh
#!/bin/bash
test(){
    echo "hello"
}

--other.sh
#!/bin/bash
./root/base.sh   ## 引入脚本
test 	## 调用引入脚本当中的test函数


-----
[root@hdp01 ~]# sh other.sh 
hello
```

# 集群自动安装软件

```
集群自动安装jdk
1.软件一致: httpd / yum
2.集群自动安装:
  2.1 一台机器自动安装(写脚本 autoInstall.sh)
  2.2 批量发送自动安装的脚本,自动执行脚本(send.sh)
  
  
准备:
 1.传输文件到http服务器
 2.编写自动下载安装的脚本
 
  
  
```

```shell
--autoInstall.sh
#!/bin/bash
#下载jdk
wget http://192.168.2.101/jdk/jdk8.tar.gz
#解压jdk
tar -zxvf jdk8.tar.gz -C /opt/
#配置环境变量
cat >> /etc/profile << EOF
export JAVA_HOME=/opt/jdk8
export PATH=$PATH:$JAVA_HOME/bin/

EOF
# 执行/etc/profile
source /etc/profile
# 测试
java -version

```


---
title: hive查询与数据类型
tags:
  - null
  - null
date: 2018-10-29 17:53:30
categories: HIVE
top:
---

# 查询常用的操作

- `where  group by    order by   having   limit   join`

1. 查询语句的写的顺序：
   ​		`select ...  from ....join....where ...group by...having...order by...limit..`

2. sql语句的执行顺序：
   ​		`from---join--where--group by---having---select---order by---limit`

# 本地模式

`set hive.exec.mode.local.auto=true;`

[本地模式参考](https://blog.csdn.net/qq_23146763/article/details/72961443)

<!-- more -->

# join关联

1. hive中连接  只支持等值连接,不支持不等值连接
   ~~`select .... from a join b on a.id>b.id;`~~  不支持 , key值不好确定
    2. hive中and连接  不支持or
       `select ... from a join b on a.id=b.id and a.pid=b.pid;`
       ~~`select ... from a join b on a.id=b.id or a.pid=b.pid;`~~  key值不好确定
3. hive支持多表关联  但是hive中进行关联的时候尽量避免笛卡尔积

## 内连接 

> inner join--inner可以省略
> 取的是两个表的交集

```
select a.id aid,a.name name,b.id bid,b.score score 
from test_a a inner join test_b b on a.id=b.id;

结果：
	1       zs      1       34
	2       ls      2       67
```

## 外链接

### 左外连接

> left outer join==left join
>
> 以join左侧的表为基础表  
>
> 左侧的表的所有数据都会显示  
>
> 右侧可以关联上的就会补全  **关联不上 null补充**

```
select a.id aid,a.name name,b.id bid,b.score score 
from test_a a left join test_b b on a.id=b.id;

结果：1,2,3
1       zs      1       34
2       ls      2       67
3       ww      NULL    NULL

```

### 右外连接

> right outer join=right join
> 以join右侧的表为基础  
>
> 左侧的表可以关联上的就会补全  **关联不上 null补充**

```
select a.id aid,a.name name,b.id bid,b.score score 
from test_a a right join test_b b on a.id=b.id;

结果：
1       zs      1       34
2       ls      2       67
NULL    NULL    4       99

```

### 全外连接

> full outer join=full join
> 两个表的并集

```
select a.id aid,a.name name,b.id bid,b.score score 
from test_a a full join test_b b on a.id=b.id;
结果：
1       zs      1       34
2       ls      2       67
3       ww      NULL    NULL
NULL    NULL    4       99
```

## 半连接

> left semi join
>
> 相当于内连接  取左半表的数据
> 左表中在右表中出现关联上的数据，判断左边在右边里面
> 左表id：1,2,3   右表中的id  ：1,2,4
>
> **hive对in/exists的支持性能很低  出现left semi join**
>
> mysql没有 半连接，有in/exists
>
> 经验：做join   小表放左侧  大表右侧  多个表进行关联的时候  先做较小表的关联 在和大表进行关联

```
select * from test_a a left semi join test_b b on a.id=b.id;

结果：
1       zs
2       ls
```

# group by 分组

> group by执行是在select之前的

1. select后面的别名, group by中**不能使用**的,只能**使用原生**的字段



2. 如果有group by   select查询的字段只能有以下两种：
   - group by的字段
   - 聚合函数

```sql
select class,min(score) from student group by class;
```





# order by  排序

>   **全局排序**  对hive表中的所有的数据进行排序

```sql
select * from stu_test01 order by age asc;
```

# sort by 排序

> 局部排序  在每一个reducetask中进行排序的
> 当reducetask的个数是一个的时候就相当于全局排序=order by

```
select * from stu_test01 sort by age asc;

reducetask=3:
结果
	reducetask0
		95008   李娜    女      18      CS
		95005   刘刚    男      18      MA
		95008   李娜    女      18      CS
		95011   包小柏  男      18      MA
		95004   张立    男      19      IS
		95014   王小丽  女      19      CS
		95019   邢小丽  女      19      IS
		95010   孔小涛  男      19      CS
		95004   张立    男      19      IS
		95019   邢小丽  女      19      IS
		95007   易思玲  女      19      MA
		95001   李勇    男      20      CS
		95012   孙花    女      20      CS
		95020   赵钱    男      21      IS
		95003   王敏    女      22      MA
		
	reducetask1
		95021   周二    男      17      MA
		95015   王君    男      18      MA
		95011   包小柏  男      18      MA
		95017   王风娟  女      18      IS
		95009   梦圆圆  女      18      MA
		95017   王风娟  女      18      IS
		95005   刘刚    男      18      MA
		95018   王一    女      19      IS
		95007   易思玲  女      19      MA
		95010   孔小涛  男      19      CS
		95014   王小丽  女      19      CS
		95018   王一    女      19      IS
		95022   郑明    男      20      MA
		95013   冯伟    男      21      CS
		
	reducetask2
		95021   周二    男      17      MA
		95015   王君    男      18      MA
		95009   梦圆圆  女      18      MA
		95002   刘晨    女      19      IS
		95002   刘晨    女      19      IS
		95006   孙庆    男      23      CS
		
sort by 在进行分每一个reducetask中的数据的时候  是随机选择的字段进行分配的
reducetask0/reducetask0 都有王小丽
```

# distribute by

> 仅仅相当于分桶的概念
>
> 将数据根据指定的distribute by**字段进行分桶**  桶的个数由**reducetask的个数决定的**

```
select * from stu_test01 distribute by age;

reducetask=3;  桶的个数3个  按照age进行分桶的

	桶1    age%3=0
		95005   刘刚    男      18      MA
		95015   王君    男      18      MA
		95011   包小柏  男      18      MA
		95017   王风娟  女      18      IS
		95008   李娜    女      18      CS
		95009   梦圆圆  女      18      MA
		95015   王君    男      18      MA
		95005   刘刚    男      18      MA
		95011   包小柏  男      18      MA
		95017   王风娟  女      18      IS
		95013   冯伟    男      21      CS
		95020   赵钱    男      21      IS
		95008   李娜    女      18      CS
		95009   梦圆圆  女      18      MA
		
	桶2   age%3=1
		95010   孔小涛  男      19      CS
		95007   易思玲  女      19      MA
		95004   张立    男      19      IS
		95019   邢小丽  女      19      IS
		95014   王小丽  女      19      CS
		95018   王一    女      19      IS
		95002   刘晨    女      19      IS
		95010   孔小涛  男      19      CS
		95007   易思玲  女      19      MA
		95004   张立    男      19      IS
		95003   王敏    女      22      MA
		95019   邢小丽  女      19      IS
		95014   王小丽  女      19      CS
		95018   王一    女      19      IS
		95002   刘晨    女      19      IS
		
	桶3 age%3=2
		95021   周二    男      17      MA
		95012   孙花    女      20      CS
		95022   郑明    男      20      MA
		95001   李勇    男      20      CS
		95021   周二    男      17      MA
		95006   孙庆    男      23      CS
```

## 分桶后局部排序

> 如果既想按照指定的字段进行分桶 distribute by 又想在每一个桶中进行局部排序 sort by
>
> **distribute by+sort by**

```sql
select * from stu_test01 distribute by age sort by age;

	blk-1   age%3=0
		95005   刘刚    男      18      MA
		95015   王君    男      18      MA
		95011   包小柏  男      18      MA
		95017   王风娟  女      18      IS
		95008   李娜    女      18      CS
		95009   梦圆圆  女      18      MA
		95015   王君    男      18      MA
		95005   刘刚    男      18      MA
		95011   包小柏  男      18      MA
		95017   王风娟  女      18      IS
		95008   李娜    女      18      CS
		95009   梦圆圆  女      18      MA
		95013   冯伟    男      21      CS
		95020   赵钱    男      21      IS
		
	blk-2  age%3=1
		95010   孔小涛  男      19      CS
		95007   易思玲  女      19      MA
		95004   张立    男      19      IS
		95019   邢小丽  女      19      IS
		95014   王小丽  女      19      CS
		95018   王一    女      19      IS
		95002   刘晨    女      19      IS
		95010   孔小涛  男      19      CS
		95007   易思玲  女      19      MA
		95004   张立    男      19      IS
		95019   邢小丽  女      19      IS
		95014   王小丽  女      19      CS
		95018   王一    女      19      IS
		95002   刘晨    女      19      IS
		95003   王敏    女      22      MA
		
	blk-3   age%3=2
		95021   周二    男      17      MA
		95021   周二    男      17      MA
		95022   郑明    男      20      MA
		95012   孙花    女      20      CS
		95001   李勇    男      20      CS
		95006   孙庆    男      23      CS
```

# hive数据类型

> hive====java中   string   int   double

## 基本数据：
- 整型：`tinyint `  `smallint ` ` int`  ` bigint`
- 浮点型：`double` `float`
- 字符串类型：`string`
- 布尔类型：`boolean`
- 时间戳：`timestamp` 

## 复杂数据类型

> 都是由基本数据类型构成的,后面都是多个数据，集合类型
>
> collection items terminated by ',' 指定集合中的元素的分割符	

###  array -- list---collection

> 应用场景：某一个**字段有多个数据** ,数据的**个数不统一**,多个数据**类型统一**

```sql
family：
name	families
zs		dad,mum,sister
ls		dad,mum
ww		dad,mum,brother,wife,son

建表：
create table test_array(name string,family array<string>) 
row format delimited fields terminated by '\t' 
collection items terminated by ',' 
stored as textfile;

加载数据：
load data local inpath '/home/hadoop/datas/family' into table test_array;

collection items terminated by ',' 指定集合中的元素的分割符	
```

* **查询：访问通过下标[]**

  ```sql
  select name,family[3] from test_array;
  ```


### map---映射   k-v

> 应用场景：k-v类型

```sql
info
name	info
zs		age:28,salary:20000,address:beijing
ls		age:20,address:sh,job:IT
ww		age:36,job:rs

建表：
create table test_map(name string,info map<string,string>) 
row format delimited fields terminated by '\t' 
collection items terminated by "," 
map keys terminated by ':';

数据加载：
load data local inpath '/home/hadoop/datas/info' into table test_map;

指定多个分割符的时候  从外向内
age:36,job:rs  先字段的分割,后k-v的分割
map keys terminated by ':'   map的key和value的分割符
```

- 查询：根据key---访问value

  ```sql
  select name,info["salary"] from test_map;
  ```


### struct

> java中的对象类型
>
> 应用场景：每一条数据都是具有严格的**统一的个数**  数据的每一个**字段都是统一的类型：类类型**

```
Student{
    string name;
    int age;
}

stu_info
name	info
zss		26,123456,shanghai,695
xh		27,234556,shenzhen,700
xl		25,678894,beijing,570

建表：
create table test_struct
(name string,info struct<age:int,xuehao:int,address:string,score:int>) 
row format delimited fields terminated by '\t' 
collection items terminated by ",";

加载数据：
load data local inpath '/home/hadoop/datas/stu_info' into table test_struct;
```

- 数据查询:通过`字段名.属性`

```
select name,info.age from test_struct;
```

# hive视图

## 特点

1. hive中的视图仅仅相当于一个**sql语句的快捷方式**

2. hive中的视图**只有逻辑视图  没有物化视图**

3. hive中的视图**不支持**`insert` `delete` `update`的操作,**只支持查询**

4. hive中的视图在查询视图的时候才会真正的执行
    `select * from age_20_view;`才开始执行的

5. hive的视图**保存在元数据库TBLS表**中，**保存的就是视图代表的sql语句**不是sql的查询结果

6. 提高hql语句的可读性 ，在很多的子查询的

  ```
  select * 
  from age_20_view    提高hql语句的可读性 
  where id >95001;
  
  
  -----------
  
  select * 
  from (select * from stu_test01 where age>20)
  where id>95001;
  ```


## 操作

1. 创建视图

   ```
   reate view view_name as select....
   create view age_20_view as select * from stu_test01 where age>20;
   ```

2. 查看视图列表

   ```
   how tables;   既有视图  又有表
   show views;  只能查看视图
   ```

3. 查看视图的详细描述信息
   ```
   desc age_20_view;
   desc extended age_20_view;
   desc formatted age_20_view;
   ```


4. 删除视图

   ```
   drop view age_20_view;  可行
   drop table age_20_view;  不可行
   ```

# hive的函数

## 函数分类

- UDF  user define function

  > 一路进一路出  处理一条数据 获取一条数据

- UDTF user define table function 

  > 一路进多路出  处理一条数据 返回多条数据 
  >
  >   explode

- UDAF user define aggregate funvtion   

  > 进多路出一路  处理多条数  返回一条数据 
  >
  >  sum  count avg max min

## 内置函数

### 内置函数操作

1. 查看所有内置函数
    `show functions; ` 271个内置函数
2. 查看内置函数的用法
    `desc function funname;`
3. 查看函数的详细描述信息~
   `desc function extended funname;`

### 常用内置函数

1. 集合函数  生成集合的函数
    `array`  数组生成函数
    `array_contains`   判断数组中是否包含元素的
    `map`  生成map集合的  参数偶数个  奇数-key 偶数--value
    `map_keys`  map的key
    `map_values`  map的value

2. 数值函数
    `round(x[, d])`  参数1：浮点数  参数2：需要保留几位小数  参数2不传  四舍五入
    `ceil`:大于参数的最小整数。`ceil(4.5)---5`
    `floor`：小于参数的最大整数`floor(4.5)---4`

3. 字符串

      ```
      # 字符串截取
          1. substr(需要截取的字符串, 截取位置[, 长度])
              字符串  从1开始
          2. substring
      
      # 字符串查找：
          1. instr(原始字符串, 需要查找的字符串)
              有  返回子字符所在的第一个字符的下标
              没有 返回 0
      
      # 字符串切分：
          1. split(原始字符串, 分割的表达式)
              返回的是切分完的数组
      
      # 字符串的拼接：
          1. concat(str1, str2, ... strN)
              多个字符串拼接为一个字符串
          2. concat_ws(各个子字符串的分隔符, [string | array(string)])
              concat_ws("-","aa","bb",array("ss","jj"))---> aa-bb-ss-jj
      # 大小写转换
          1. lcase  小写
          2. ucase   大写
          3. lower  小写
          4. upper   大写
      
      # 字符串判断：  处理null
          1. nvl(查询的字段,默认值)  
              参数1为null则返回参数2  
              参数1不为null  返回参数1
              select name,nvl(family[2],"self") from test_array;
      
          2. if（表达式，返回值1，返回值2）
              表达式？返回值1：返回值2
              select if(family[2] is null,"self",family[2]) from test_array;
      
          3. is null 判断是否为null  
              是  true  
              不是false
      
      # 去除空格
          1. trim 去二端空格
          2. ltrim 去左端空格
          3. rtrim 去右端空格
      ```

4. 日期操作

   ```
   1. unix_timestamp(date, [pattern])
   	将给定日期转换为时间戳
       select unix_timestamp();  返回的当前系统的时间戳
       select unix_timestamp("2018-10-20","yyyy-MM-dd");返回给定日期的时间戳
    
    2. from_unixtime(系统时间戳, 需要转换的日期格式)
       将时间戳-->日期的
       
       year  返回日期或时间戳所在年份					
       month  获取月份的
       day   获取天的
       hour
       minute
       second
       weekofyear(date)  返回的是一年中的第几周
       datediff(date1, date2)  返回的是(date1 大  date2小)之间相差的天数
   ```

5. 表生成函数

   > 应用场景:细分

   ```
   explode（数组/map集合）  炸裂函数   进一条数据出来多条数据
       参数：数组/map集合
       将数组或map炸裂为多行  一个元素一行
   
   语法上不支持explode和普通字段一起查询
   select name,explode(family) from test_array;--->不支持
   
   解决方案：
   
   select name,fs.*
   from test_array 
   lateral view explode(family) fs;
   
   [1,2,3,4]----4行
       1
       2
       3
       4
   
   [1:2,3:4]----2行
       key	value
       1  2
       3  4
   ```

   ```
   应用场景：
   数据
   		电影名		电影类型：
   		分手大师   		喜剧
   		分手大师		爱情
   		一出好戏	喜剧/悬疑/惊悚/爱情
   获取电影名 电影类型（一个）
   
   select name,fs.family_mem 
   from test_array 
   lateral view explode(family) fs as family_mem;
   	fs 未炸裂之前的字段别名
   	family_mem 炸裂之后的字段
   
   select name,stuin.sk,stuin.sv 
   from test_map 
   lateral view explode(info) stuin as sk,sv;
   	stuin 未炸裂之前的字段别名
   	sk,sv map炸裂之后的字段k-v
   
   ```

## 自定义函数

开发步骤：
1. 创建一类继承hive的UDF类

2. 实现一个方法 名字必须叫`evaluate`

     > 返回值: 函数调用的返回值
     >   参数:  函数调用的时候的参数 

3. 将上面的程序进行打jar包 传到linux本地

4. 将jar包放在hive的classpath下

     > `add jar path`;  将本地jar添加到hive的classpath下
     >   ad d jar /home/hadoop/myudf.jar;

     >  验证：`list jars;`  ` list jar;`
     >   当前添加的jar包的作用域：当前客户端   

     >  如果当前客户端退出 jar包失效 下次进入的时候重新添加

5. 创建一个临时函数 注册函数 关联自定义的函数类

     > `create temporary function myfun as 'com.ghgj.cn.test01.MyUDF';`
     >
     > as 后面必须是全限定名
     >
     > ` show functions;`
     >   临时函数只对当前的客户端生效  客户端退出  临时函数失效

6.  使用函数

     > 通过函数名---》com.ghgj.cn.test01.MyUDF---》evaluate(int a,int b,int c) evaluate(String ip)
     > 调用的时候传入不同的参数调用不同的evaluate方法
     > ​	select myfun(1,2,3);
     > ​	select myfun("1.23.4.220");

# json解析

```
Stu{
    id int;
    name string;
    age int;
    score int[];
}
json数据：{id:1,name:"zs",age:35,score:[12,34,1]}
```

## 需要解析json数据：

   1. 自定义函数
  2. 使用内置函数

     - `get_json_object(json_txt, path)`
       参数1：json格式的字符串
       参数2：需要解析的属性的路径

       ```
       #  $   : Root object
         json串的根目录  最外层的目录
       #  .   : Child operator
         用来取子节点   key-value格式的子节点
       #  []  : Subscript operator for array
         用来取数组的元素  用下标进行取的
       #  *   : Wildcard for []
       ```

## 案例

```json
数据：
[{"movie":"914","rate":"3","timeStamp":"978301968","uid":"1"}]
1. $ ----> []
2. $[0].movie---->914

建表：
    movie	rate	timeStamp	uid
    914		3		978301968	1
		
select 
get_json_object('{"movie":"914","rate":"3","timeStamp":"978301968","uid":"1"}',
'$.movie');
```

```json
var data = [{
    "id": 1,
    "startTime": "2017-12-12 10:36:50",
    "endTime": "2018-02-02 00:00:00",
    "value": "0.26",
    "jobCode": "zd_test_02_171212103650",
    "status": "正常",
    "machCode": 1
},
{
    "id": 2,
    "startTime": "2018-01-03 00:00:00",
    "endTime": "2018-01-12 00:00:00",
    "value": "0.66",
    "jobCode": "close_test_05_171212103622",
    "status": "调机",
    "machCode": 3
}];
$[1]==={
    "id": 2,
    "startTime": "2018-01-03 00:00:00",
    "endTime": "2018-01-12 00:00:00",
    "value": "0.66",
    "jobCode": "close_test_05_171212103622",
    "status": "调机",
    "machCode": 3
}]


$[1].jobCode=="close_test_05_171212103622"

```


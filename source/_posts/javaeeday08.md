---
title: javaeeday08
tags:
  - null
  - null
date: 2018-09-02 22:14:19
categories:
top:
---



# JavaScript

## javascript和java的一些区别：

	1). javascript是一个解释性语言,java是编译解释性语言
	2). javascript是一个弱势语言，Java是一个强势语言
	3). 在页面上引入的方式不同javascript代表用<script>引入，Java代码<%>
	4). JS是基于对象，Java是面向对象。
## JavaScript语言的组成：

```text
EcMAScript  + BOM + DOM 
	ECMAScript: 规定了一些语法，变量，for循环等等结构
    BOM: Browser  object  Model 浏览器对象模型
    DOM: Document  object  Model 文档对象模型
```
<!-- more -->

## JavaScript与Html的结合方式(掌握)

	Javascript与HTML的结合方式有三种：
		1.采用事件来调用，代码写在字符串中
			<button onclick = "alert('大家好')">点击</button>
		2.采用定义函数的方式: 用function来定义函数 
			function fun(){ alert('你好')} ;
		3.采用外部js文件.
			利用<script src = "a.js"></script>引入
## JavaScript基本语法(掌握)

### 定义变量

> 采用var关键字来定义.定义的变量的类型是由给定的值来决定的。

### 数据类型： 

1. undifined,表示未定义类型。
2. Number类型。代表了一切数字类型
3. String类型。字符串类型
4. Boolean类型。布尔类型
5. Function类型。函数类型
  . Null类型。	  

### 判断变量的类型

1. 采用typeof 函数判断：typeof(a) =="Sttring"
2. 采用instanceof 运算符；

### 三大结构

1. 顺序结构
2. 选择结构
3. 循环结构for ,while ,do...while

### 运算符

1. 一元运算符 +(正号) -  ++ -- 
  2. 二元运算符 +(加法) - * / %
 3. 三元运算符 ？ ：
4. 等号
   - == 判断的是内容,
   - === 全等于 ,判断类型和内容

### 类型的转换
	1. Number转String : 3 + ""
	2. Number转Boolean ：在javascript中，非0为真，0为假。如果变量为null或者undefined，也为假.
	3. String转Number:
		a. parseInt,parseFloat
		b. 乘以1即可
## JavaScript 函数

### 定义（掌握）

	函数的定义有三种方式： 
		1.采用function关键字来定义
		2.采用匿名的方式来定义
		3.采用new Function()的方式(了解,不推荐)
```javascript
function fun(){
    alert("大家好") ;
}

//  fun() ;

var a = function(){
    alert("我是匿名函数") ;
}

// a() ;

var b = new Function("x","y","z","alert(x+y+z)") ;
小括号中最后一个参数是函数体，之前所有的参数都是形参.
// b(3,4,5) ;
```

###  函数的调用

> 没有重载的概念

1. 函数调用的时候参数可以传递多个，可以和函数定义的形参个数不符合
2. 如果函数重名了，那么调用的时候一定是调用最后一个，与参数无关。

### 函数的劫持

> 改变函数本身的作用.改变javascript的**预定义的函数**预定义好的功能

```javasc
<script type="text/javascript">
	
		//函数劫持:改变javascript的预定义的函数预定义好的功能
		window.alert = function(x){
			document.write(x) ;
		}
			alert("abc") ;

		/*var a = function(){
			alert("1") ;
		}

		a = function(){
			alert("2") ;
		}*/   //不是函数劫持
	
	</script>
```

## JavaScript 全局函数(掌握)

1. isNaN (掌握)：用来判断变量是否是数字类型的字符串
      NaN: not a Number ,不是一个数字

   > 返回TRUE不是数字

  2. parseInt,parseFloat

 3. eval(掌握): 把字符串转换成数字

     a.主要执行字符串,将结果转换为数字
     	b.将json格式的字符串转换为json

     		{"a":"中国","b":"美国","c":"日本"}

4. escape(): 编码

5. unescape(): 解码

6. encodeURI(): 对网址（URL）进行编码

7. decodeURI(): 对网址（URL）进行解码

```javascript
if(isNaN(a)){
    alert("不是数字") ;
}else
    alert("是数字") ;

-----------------
alert(eval("3 + 10") + eval("2")) ;//15

---------------------
    
var b = "中国" ;
var c = escape(b) ;
alert(c) ;	//%u4E2D%u56FD
alert(unescape(c)) ;	//中国

---------------------------------------
var e = "http://www.sohu.com?a=中国&b=美国" ;
var f = encodeURI(e) ;
alert(f) ;	//http://www.sohu.com?a=%E4%B8%AD%E5%9B%BD&b=%E7%BE%8E%E5%9B%BD
alert(decodeURI(f)) ;
```

## JavaScript常用对象（掌握）

### Array对象 

>  数组对象，进行数组操作
>
>  typeof(数组名):得到的是object



> var arr = new Array() ;  //定义一个数组arr,初始长度为0
>
> var arr1 = new Array(4) ;  //定义一个数组arr1,初始长度是4
>
> var arr2 = new Array(1,2,3,4,5) ; 
>
> //定义一个数组arr2，初始化数据是1,2,3,4,5

> var arr3 = [] ;  //定义了一个数组，里面是空的
> var arr3 = [3,2,4,5] ;  //定义了一个数组，同时初始化数据

1. 定义方式
      1.采用new的方式
      			2.采用中括号[]来定义
      				**数组的长度可以随时改变**
      				

      	特点： 
      		1.javascript中数组的大小可以随时改变
      		2.javascript中数组的下标可以是任意对象。

```javascript
var arr = new Array() ;  //定义一个数组arr,初始长度为0
var arr1 = new Array(4) ;  //定义一个数组arr1,初始长度是4
/*arr1[0] = 1 ;
		arr1[1] = 10 ;
		alert(arr1[2]) ;   //弹出来undefined,没有初始化
		alert(arr1[100]);  //相当于定义了一个变量arr1[100],没有赋值*/

//-----------------------

var arr3 = [] ;  //定义了一个数组，里面是空的
var arr3 = [3,2,4,5] ;  //定义了一个数组，同时初始化数据

//改变数组的的长度
alert(arr4.length) ;
//arr4.length = 100 ;  //将数组的长度变为100
//alert(arr4.length) ;

//arr4[100] = 100 ;  //将数组的长度变为101
//alert(arr4.length) ;

arr4.length = 2 ;   //将数组的长度变为2，多于的数据将消失了
//alert(arr4[2]) ;   //弹不出原来的数据了，弹出来undefined


//--数组下标的访问------------------
var arr5 = ["中国","美国","日本"] ;
arr5["中国"] = ["北京","上海","天津"] ;
alert(arr5["中国"][0]) ; //北京

```

#### 与java中数组的差别：

	a. 
		Java中数组是有类型的，意味着一旦类型确定，则数组中所有的数据都是同一种类型。
		javascript中数组时没有类型的，意味着数组中的数据可以存放任意类型 (不推荐,取出转换麻烦)
	b. 
		java中数组的长度一旦确定就不能再更改了
		javascript中数组的长度是可以变化的(扩大缩小都可以)
		变长的两种办法 : 
		1) 指定length属性的值
		2) 指定某个数组中元素的值
	c. 
		java中的数组的数据引用必须用下标引用，小标必须是整数.
		javascript中数组的数据引用可以用任意对象
#### 方法

> 方法： 
> 		1.join() : 把数组的所有元素放入一个字符串. 默认用逗号连接
> 		2.push() : 向数组的末尾添加一个元素
> 		4.reverse() :反转
> 		3.shift() : 删除并返回数组的第一个元素
> 		4.sort() ; 排序 .默认同类型的数据相比较.
> 			默认情况下先将能转换为number类型的字符串和number类型的放一起比较(转为string类型比较)
>             转换不成的作为一组进行比较
>             如果想按自己的规则进行比较，那么需要传递一个function类型的参数制定比较规则。

```javascript
var arr =  ["中国","美国","日本"] ;
//alert(arr.join()) ;  //默认用逗号连接
alert(arr.join("")) ;   //用空字符串连接

arr.push("韩国") ;
alert(arr.join()) ;

arr.reverse() ;
alert(arr.join()) ;

alert(arr.shift()) ; 


//---------------------
var arr1 = [3,8,"23","34",123,"abc","ab"] ;
//alert(arr1.sort()) ;		
//123,23,3,34,8,ab,abc(转为string类型比较)
alert(arr1.sort(function(a,b){    
    //升序排序
    //传递一个function类型参数，制定我们的比较规则
    if(a *1  > b*1)
        return 1 ;
    else
        return -1 ;
})) ;
```

### String对象

#### 方法

String对象的方法：
1. substr():  截取子字符串,两个参数，第一个参数是下标，第二个参数是截取的长度
2. substring(): 截取子字符串，两个参数，代表的是下标
3. charAt():拿到指定位置的字符
4. split():切割
5. fontcolor():使用指定的颜色显示字符串
6. fontsize():
7. link():将字符串显示为超链接
8. big();
9. bold();字体变粗(一次)

> 属性:length



```JavaScript
var s = "abcdefg" ;
//alert(s.substring(2,3)) ;//c
//alert(s.substring(3,2)) ;//c

当大于长度,按最大
当为负数,按最小
位置可相反
// alert(s.substring(300,-200)) ;	//abcdefg

alert(s.substr(2,3)) ;   //3是i长度，不是索引 cde
```

### Math对象

> 执行数学任务

1. ceil():向上取整
2. floor():向下取整
3. min(x,y):最小值
4. max(x,y):最大值
5. random():0~1之间的随机数(可以等于0，永远不能取得1)
6. round(x):四舍五入
7. pow(x,y):x的y次幂

```javascript
var a = 3.1;
alert(Math.floor(a)) ;	//3
alert(Math.ceil(a)) ;	//4
alert(Math.round(a))	//3
```

### Date对象

> 用于处理日期和时间
>
> Date对象： 代表一个时间
> 				方法： getXXX() : 拿到年月日,时分秒

```javascript
var d = new Date() ;
alert(d.toLocaleString()) ;	//转换为11
alert(d.getYear()) ;	//返回的是 "当前年份-1900" 的值（即年份基数是1900）
alert(d.getFullYear());	//获取正确的4位年份
alert(d.getMonth());	//月:0-11
alert(d.getDate()) ;	//天
alert(d.getDay()) ;		//0-6;星期天位0
alert(d.getHours());	0-23
alert(d.getMinutes());
alert(d.getSeconds());
```

### RegExp正则表达式对象

>  RegExp 对象正则表达式对象 

写法： 

         			1.  new的方式   var r = new RegExp("ab") ;
                			2.   采用/正则表达式/ (推荐)  var r = /ab/ ;

```javascript
var reg = /(..)./ ;  //括号表示子匹配，就是对结果进一步匹配
var s = "abcde" ;

alert(reg.test(s)) ;  //测试字符串中是否包含正则表达式中所匹配的字符串,返回的是boolean类型的
alert(reg.exec(s).length) ;  //以数组的形式返回匹配的正则表达式的字符串 2
alert(reg.exec(s)[0] + ":" + reg.exec(s)[1]) ; abc: ab(对abc再次进行匹配)
```



# 出现的问题

## innerHTML和innerText的区别

1. innerHTML**必须是有开始标签和结束标签**的标签对象才能使用

   1. 获取开始标签和结束标签之间的内容

2. innerText:获取的是文本

3. 对于input

   ```html
   //拿到文本框中的内容
   var txt = form.username.value ; 
   
   姓名:<input type="text" name="username"><span id = "sname"></span><br>
   ```

# 案例

## 标题栏的滚动

```javascript
<body onload="init()">
    <script type="text/javascript">

        //示例：标题栏的滚动
        function init() {
        //1.拿到标题栏的文本
        var title = document.title;
        //alert(title) ;
        //2.将文本字串转换为数组
        var arr = title.split("");
        //3.拿到数组的第一个元素，并从数组中删除
        var first = arr.shift();
        //4.将第一个元素添加到数组的最后
        arr.push(first);
        //5.将数组再组合成一个字符串
        title = arr.join("");
        //6.将字符串再赋值回标题栏
        document.title = title;
        //7.每隔1秒做一遍前6步
        setTimeout("init()", 1000);
    	}
   </script>
</body>
```

## 字体变变变

```javascript
function fun(){
    //1.拿到p标签对象
    var p = document.getElementById("p") ;
    //2.拿到p标签对象的主体内容
    var txt = p.innerHTML ;   //innerHTML必须是有开始标签和结束标签的标签对象才能使用
    //3.改变字体内容，再赋值回去
    p.innerHTML = txt.big().big() ;
}

<p id = "p">大</p>
<input type="button" value="变变变" onclick="fun()">
```

## 字体颜色随机变换

```JavaScript
var arr = ["red","blue","green","yellow","#666666"] ;
function fun1(){
    //1.拿到p标签对象
    var p = document.getElementById("p") ;
    //2.随机取得一个整数作为数组的下标
    var index = Math.floor(Math.random()*arr.length) ;
    //3. 拿到p标签对象的主体内容
    // var txt = p.innerHTML ;
    var txt = p.innerText ;
    //3.给p标签对象的主体内容改变颜色，并赋值回去
    p.innerHTML = txt.fontcolor(arr[index]) ;
    // alert(p.innerHTML) ;

    setTimeout("fun1()",500) ;
}

<p id = "p">大</p>
<input type="button" value="变变变1" onclick="fun1()">
```



----------------------



# BOM

> browser object modal :浏览器对象模型。
>
> 浏览器对象：window对象。
>
>  Window 对象会在 <body> 或 <frameset> 每次出现时被自动创建。

## windows对象

### window的属性

1. innerHeight: 返回文档显示区的高度  ( IE不支持)
2. innerWidth: 返回文档显示区的宽度( IE不支持)
    		通用写法：
       1.  document.body.clientWidth
           2.  document.body.clientHeigh
3. outerheight  包括了工具栏，菜单栏等的高度
4. outerwidth   包括滚动条的宽度

```javas
function init(){
    var x = window.document.body.clientWidth ;
    var y = window.document.body.clientHeight ;
    alert(x + ":" + y) ;
}
```



1. status：设置窗口状态栏的文本。

```javascript
<script>
		function init() {
			//拿到当前时间
			var d = new Date();
			//设置状态栏的文本
			self.status = d.toLocaleString();

			setTimeout("init()", 1000);
		}
	</script>

	<body onload="init()">
	</body>
```

1. windows的三种对话框
       1)消息框 alert() ;
       2)确认框 confirm() ;返回Boolean类型的值
       3)输入框 prompt() ; 返回输入的字符串(了解)

```JavaScript
   <script>
       window.alert("你好");
   
   while(true) {
       if(confirm("你爱我吗？") == false)
           continue;
       break;
   }
   
   var a = prompt("请输入年龄：", 12); //默认显示12
   alert(a);
   </script>
```

1. windows对象的计时器
   1. setTimeout(): 隔一段时间调用某个函数(只调用一次),返回的是一个计时器(理解成一个手表)
      clearTimeout() ：销毁由setTimeout()产生的计时器
          2. setInterval(): 每隔一段时间调用某个函数(多次调用) clearInterval(): 销毁由setInterval()产生的计时器
2. windows的打开
   1. self :等同于window对象
   2. parent：是打开窗口的父窗口对象
   3. opener：是打开窗口的父窗口对象。
   4. frames[]: 数组类型，代表子窗口的window对象的集合,可以通过frames[索引]拿到子窗口的window对象。



> 2种情况下使用opener:
> 				   1.使用winodw.open()方法打开的页面
> 				   2.超链（里面的target属性要设置成_blank）
>
>

> 2种情况下使用parent:
> 				   1.iframe 框架
> 				   2.frame 框架

**子窗口中的文本框中的数据传递到父窗口中的文本框中显示**

1. 使用open（）打开

```html
//父窗口，使用open打开子页面
function fun(){
	window.open("sub.html") ;
}

<body>
    <input type="text" name="" id = "txt"> 
    <input type="button" value="打开sub.html页面" onclick="fun()">
    <a href = "sub.html" target = "_blank">打开sub.html页面</a>
</body>

```

```html
//子窗口sub.html
<script type="text/javascript">
    //示例： 将子窗口中的文本框中的数据传递到父窗口中的文本框中显示
    function fun(){
        //1.拿到文本框中填写的数据
        var v = document.getElementById("txt").value ;
        //2.拿到父窗口对象
        var w = window.opener ;
        //3.拿到父窗口中的文本框对象
        var txt = w.document.getElementById("txt") ;
        //4.将内容赋值给父窗口中的文本框对象的value属性
        txt.value = v ;
    }
</script>

<input type="text" name="" id = "txt">
<input type="button" value="传递子窗口的值到父窗口中的文本框" onclick="fun()">


```

1. 使用框架

```html
//父窗口

function fun(){
//1.拿到文本框中填写的数据
var v = document.getElementById("txt").value ;
//2.拿到子窗口对象
var w = window.frames[0];
//3.拿到子窗口中的文本框对象
var txt = w.document.getElementById("txt") ;
//4.将内容赋值给父窗口中的文本框对象的value属性
txt.value = v ;
}

姓名：<input type="text" name="" id = "txt">
<input type="button" value="传递数据到子窗口中" onclick="fun()">
<iframe src = "sub.html"></iframe>
```

```html
//子窗口
function fun(){
//1.拿到文本框中填写的数据
var v = document.getElementById("txt").value ;
//2.拿到父窗口对象
var w = window.parent;
//3.拿到父窗口中的文本框对象
var txt = w.document.getElementById("txt") ;
//4.将内容赋值给父窗口中的文本框对象的value属性
txt.value = v ;
}

<input type="text" name="" id = "txt">
<input type="button" value="传递数据到父窗口中" onclick="fun()">
```



### 方法

1. close() : 关闭页面

```JavaScript
function clo(){
    window.close() ;
}

<button onclick="clo()">点击关闭页面</button>
```



1. open方法，是打开一个页面.

   > window.open(URL,name,features,replace)



|   URL    | 一个可选的字符串，声明了要在新窗口中显示的文档的 URL。如果省略了这个参数，或者它的值是空字符串，那么新窗口就不会显示任何文档。 |
| :------: | :----------------------------------------------------------- |
|   name   | 一个可选的字符串，该字符串是一个由逗号分隔的特征列表，其中包括数字、字母和下划线，该字符声明了新窗口的名称。这个名称可以用作标记 <a> 和  <form> 的属性 target 的值。如果该参数指定了一个已经存在的窗口，那么 open()  方法就不再创建一个新窗口，而只是返回对指定窗口的引用。在这种情况下，features 将被忽略。 |
| features | 一个可选的字符串，声明了新窗口要显示的标准浏览器的特征。如果省略该参数，新窗口将具有所有标准特征。在窗口特征这个表格中，我们对该字符串的格式进行了详细的说明。 |
| replace  | 一个可选的布尔值。规定了装载到窗口的 URL 是在窗口的浏览历史中创建一个新条目，还是替换浏览历史中的当前条目。支持下面的值：  true - URL 替换浏览历史中的当前条目。  false - URL 在浏览历史中创建新的条目。 |

> windows Features 窗口特征

| channelmode=yes\\no\\1\\0 | 是否使用剧院模式显示窗口。默认为 no。                        |
| ------------------------- | ------------------------------------------------------------ |
| directories=yes\\no\\1\\0 | 是否添加目录按钮。默认为 yes。                               |
| fullscreen=yes\\no\\1\\0  | 是否使用全屏模式显示浏览器。默认是 no。处于全屏模式的窗口必须同时处于剧院模式。 |
| height=pixels             | 窗口文档显示区的高度。以像素计。                             |
| left=pixels               | 窗口的 x 坐标。以像素计。                                    |
| location=yes\\no\\1\\0    | 是否显示地址字段。默认是 yes。                               |
| menubar=yes\\no\\1\\0     | 是否显示菜单栏。默认是 yes。                                 |
| resizable=yes\\no\\1\\0   | 窗口是否可调节尺寸。默认是 yes。                             |
| scrollbars=yes\\no\\1\\0  | 是否显示滚动条。默认是 yes。                                 |
| status=yes\\no\\1\\0      | 是否添加状态栏。默认是 yes。                                 |
| titlebar=yes\\no\\1\\0    | 是否显示标题栏。默认是 yes。                                 |
| toolbar=yes\\no\\1\\0     | 是否显示浏览器的工具栏。默认是 yes。                         |
| top=pixels                | 窗口的 y 坐标。                                              |
| width=pixels              | 窗口的文档显示区的宽度。以像素计。                           |

```JavaScript
function fun(){

    window.open("sub.html","","width=200,height=200,status=no,titlebar=no,menubar=no,toolbar=no,resizable=0") ;
}
```

1. 模态窗体
   - 模态类型对话框：就是指除非采取有效的关闭手段，用户鼠标点或者输入光标一直停留在其上的 对话框。 
   - 非模态类型对话框：不会强制此特性，用户可以在当前对话框以及其他敞口间进行切换。
2. showModalDialog() (IE 4+ 支持) 　  
3. showModelessDialog() (IE 5+ 支持)
4. window.showModelessDialog()方法用来创建一个显示HTML内容的模态对话框。

## history对象

> 对象存储了访问过的页面

|   方法   | 描述                              |
| :------: | --------------------------------- |
|  back()  | 加载 history 列表中的前一个 URL   |
| orward() | 加载 history 列表中的下一个 URL   |
|   go()   | 加载 history 列表中的某个具体页面 |

|  属性  | 描述                            |
| :----: | ------------------------------- |
| length | 返回浏览器历史列表中的 URL 数量 |

## location对象

> 掌握：
>
> 1. href属性
>
>    ```
>    	  2. reload()方法：重新加载本页面
>    ```

# 事件

## 常用事件

> Event 对象代表事件的状态，比如事件在其中发生的元素、键盘按键的状态、鼠标的位置、鼠标按钮的状态。

1. 鼠标移动事件
   onmousemove(event) : 鼠标移动事件 event是事件对象。名字固定 
   	onmouseover : 鼠标悬停事件
   	onmouseout : 鼠标移出事件

2. 鼠标点击事件
   onclick 

3. 加载与卸载事件 

   1. img, 和widows
   2. onload ,onunload

4. 聚焦与离焦事件

   1. onfocus, onblur
   2. focus():获得焦点

   ```javascript
   function fun(obj){
       obj.style.border = "1px solid red " ;
       obj.style.backgroundColor = "#ff66ff" ;
       obj.style.color = "green" ;
   }
   
   function fun1(obj){
       if(obj.value == ""){
           alert("内容不得为空") ;
           //obj.focus() ;  //获得焦点
       }
   }
   <input type="text" name="" onfocus = "fun(this)" onblur = "fun1(this)">
   ```

5. 键盘事件
   onkeypress,onkeyup,onkeydown

   ```javascript
   function fun(obj,e){
       //拿到按键的asc码
       obj.value = e.keyCode ;		
   }
   
   
    <input type="text" name="" onkeypress = "fun(this,event)">
   ```

6. 提交与重置事件

   1. onsubmit,onreset
   2. 位置:form表单的`<form>`标签内,必须要return
   3. onsubmit = "return check(this)"
   4. onreset = "return fun(this)"

   ```html
   function check(form){
   //拿到文本框中的内容
   var txt = form.username.value ; 
   //判断内容
   	if(txt == ""){
   		document.getElementById("sname").innerHTML = " <font color = red>*  姓名必须填写</font>" ;
   		form.username.focus() ;
   		return false; 
   	}
   
   	return true ;
   }
   		
   function fun(form){
   	alert("重置事件") ;
   	return true ;
   }
   
   <form method="post" action="01-鼠标的单击事件.html" onsubmit = "return check(this)" onreset = "return fun(this)">
       姓名:<input type="text" name="username"><span id = "sname"></span><br>
       <input type="submit" value = "提交">
       <input type="reset" value = "重置">
   </form>
   ```

7. 选择与改变事件

   1. onselect = "fun(this)" 
   2. onchange = "fun1(this.value)"
      1. 文本框: 内容发生改变,失去焦点
      2. 下拉框: 下标发生改变
         1. onchange = "fun2(this.value,this.selectedIndex)"
      3. 多行文本框

```html
function fun(obj){
    alert(obj.value) ;
}

function fun1(v){
    alert(v) ;
}

function fun2(v,index){
    alert(v + ":" + index) ;
}

<input type="text" name="" onselect = "fun(this)" onchange = "fun1(this.value)" >
<select onchange = "fun2(this.value,this.selectedIndex)">
    <option value = "china">中国</option>
    <option value = "america"> 美国</option>
    <option value = "japan">日本</option>
</select>
```





## 事件句柄　(Event Handlers)

| 属性                                      | 此事件发生在何时...                  |
| ----------------------------------------- | ------------------------------------ |
| [onabort](event_onabort.asp.html)         | 图像的加载被中断。                   |
| [onblur](event_onblur.asp.html)           | 元素失去焦点。                       |
| [onchange](event_onchange.asp.html)       | 域的内容被改变。                     |
| [onclick](event_onclick.asp.html)         | 当用户点击某个对象时调用的事件句柄。 |
| [ondblclick](event_ondblclick.asp.html)   | 当用户双击某个对象时调用的事件句柄。 |
| [onerror](event_onerror.asp.html)         | 在加载文档或图像时发生错误。         |
| [onfocus](event_onfocus.asp.html)         | 元素获得焦点。                       |
| [onkeydown](event_onkeydown.asp.html)     | 某个键盘按键被按下。                 |
| [onkeypress](event_onkeypress.asp.html)   | 某个键盘按键被按下并松开。           |
| [onkeyup](event_onkeyup.asp.html)         | 某个键盘按键被松开。                 |
| [onload](event_onload.asp.html)           | 一张页面或一幅图像完成加载。         |
| [onmousedown](event_onmousedown.asp.html) | 鼠标按钮被按下。                     |
| [onmousemove](event_onmousemove.asp.html) | 鼠标被移动。                         |
| [onmouseout](event_onmouseout.asp.html)   | 鼠标从某元素移开。                   |
| [onmouseover](event_onmouseover.asp.html) | 鼠标移到某元素之上。                 |
| [onmouseup](event_onmouseup.asp.html)     | 鼠标按键被松开。                     |
| [onreset](event_onreset.asp.html)         | 重置按钮被点击。                     |
| [onresize](event_onresize.asp.html)       | 窗口或框架被重新调整大小。           |
| [onselect](event_onselect.asp.html)       | 文本被选中。                         |
| [onsubmit](event_onsubmit.asp.html)       | 确认按钮被点击。                     |
| [onunload](event_onunload.asp.html)       | 用户退出页面。                       |

## 鼠标 / 键盘属性

| 属性                                          | 描述                                         |
| --------------------------------------------- | -------------------------------------------- |
| [altKey](event_altkey.asp.html)               | 返回当事件被触发时，"ALT" 是否被按下。       |
| [button](event_button.asp.html)               | 返回当事件被触发时，哪个鼠标按钮被点击。     |
| [clientX](event_clientx.asp.html)             | 返回当事件被触发时，鼠标指针的水平坐标。     |
| [clientY](event_clienty.asp.html)             | 返回当事件被触发时，鼠标指针的垂直坐标。     |
| [ctrlKey](event_ctrlkey.asp.html)             | 返回当事件被触发时，"CTRL" 键是否被按下。    |
| [metaKey](event_metakey.asp.html)             | 返回当事件被触发时，"meta" 键是否被按下。    |
| [relatedTarget](event_relatedtarget.asp.html) | 返回与事件的目标节点相关的节点。             |
| [screenX](event_screenx.asp.html)             | 返回当个某事件被触发时，鼠标指针的水平坐标。 |
| [screenY](event_screeny.asp.html)             | 返回当个某事件被触发时，鼠标指针的垂直坐标。 |
| [shiftKey](event_shiftkey.asp.html)           | 返回当事件被触发时，"SHIFT" 键是否被按下。   |

# js修改css

1. 对象.style

   > css样式有- 的,需要将-去掉,大写后面的第一个

```javascript
function fun(){
    //拿到p标签对象
    var p = document.getElementById("p") ;
    //定义p的样式
    //p.style.color = "red" ;
    //p.style.border = "5px dashed green" ;
    
    p.style.backgroundColor = "red" ;
}
```

1. 写一个css,类选择器(推荐)

```JavaScript
<style type="text/css">
    .one{
        color:red ;
        border:6px solid green ;
        cursor:hand;
    }
</style>

//拿到p标签对象
var p = document.getElementById("p") ;
p.className = "one" ;//对象会应用选择器的样式
p.className = "" / "none"; //取消样式 
```

# 省市联动

```javascript
<script>
    var arr = ["中国", "美国", "日本"];

arr["中国"] = ["北京", "上海", "钓鱼岛"];
arr["美国"] = ["纽约", "华盛顿", "旧金山"];
arr["日本"] = ["东京", "大阪", "神户"];

arr["北京"] = ["海淀", "朝阳", "昌平", "丰台"];
arr["上海"] = ["浦东", "金山", "崇明", "浦西"];
arr["钓鱼岛"] = ["钓鱼岛东", "钓鱼岛南", "钓鱼岛西", "钓鱼岛北"];

arr["纽约"] = ["纽约1", "纽约2", "纽约3", "纽约4"];
arr["华盛顿"] = ["华盛顿1", "华盛顿2", "华盛顿3", "华盛顿4"];
arr["旧金山"] = ["旧金山1", "旧金山2", "旧金山3", "旧金山4"];

arr["东京"] = ["东京1", "东京2", "东京3", "东京4"];
arr["大阪"] = ["大阪1", "大阪2", "大阪3", "大阪4"];
arr["神户"] = ["神户1", "神户2", "神户3", "神户4"];

function init() {
    //填充国家
    fillData(arr, "country");

    //填充省市
    fillData(arr[arr[0]], "province");
    //填充地区
    fillData(arr[arr[arr[0]][0]], "area");
}

function fillData(arr, id) {
    //清空select选项
    document.getElementById(id).options.length = 0;
    //添加选项
    for(var i = 0; i < arr.length; i++) {
        //创建一个option对象
        //第一种
        /* var option = new Option() ;
					 option.text = arr[i] ;
					 option.value = arr[i] ;*/

        //第二种
        var option = new Option(arr[i], arr[i]);

        //将option对象添加到select中
        document.getElementById(id).options.add(option);
    }
}

function changePro(coun) {

    //添加省市
    fillData(arr[coun], "province");

    //添加地区
    fillData(arr[arr[coun][0]], "area");
}

function changeArea(pro) {
    //改变地区
    fillData(arr[pro], "area");

}
</script>

<body onload="init()">
    国家：
    <select id="country" onchange="changePro(this.value)"></select>
    省市：
    <select id="province" onchange="changeArea(this.value)"></select>
    地区：
    <select id="area"></select>
</body>
```


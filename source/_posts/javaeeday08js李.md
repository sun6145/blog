---
title: javascript重复
tags:
  - null
  - null
date: 2018-09-02 22:14:19
categories:
top:
---


# 函数

```javascript
function 函数名称(参数列表){
    函数体
}

无参无返回值:
function calc(){
    js…
}
调用:
calc();

有参有返回值
function calc2(a,b){
    return a+b;
}
calc2(1,2);


匿名函数
function(){
    js…
}
//匿名函数创建和调用
(function(a,b){
    console.log(a+b);
})(1,2);

```

<!-- more -->

## 内置函数

1. parseInt();将字符串转换为整形的数据
2. parseFloat():将字符串转换为浮点数
3. isNaN(():是否是一个非数字(字符串数值,数值,false:是数值)
4. eval():将字符串转换为js代码



# 对象

## window

> 表示浏览器窗口
>
> 直接定义全局的函数都是window的函数
>
> window对象的函数或对象可以省略window关键字

### 属性

1. document:获取文档对象
2. innerheight:获取显示区高度
3. innerwidth:获取显示区的宽度

### 方法



1. open(url,窗口名字,弹出窗口的特征(位置,大小等)):打开窗口,返回一个window对象.
2. close():关闭窗口
3. alert():警告框
4. confirm():确认框,返回boolean
5. prompt: 输入框
6. setTimeout(function,delaytime): 延时一段时间后,执行函数只执行一次,返回number类型
7. clearTimeout(id);清楚timeout
8. setInterval(function,delaytime):周期性执行,返回类型
9. clearInterval(intervalId);清除interval

```JavaScript
console.log(window.innerHeight);
console.log(window.innerWidth);

//打开新窗口
var newWindow =window.open("http://www.baidu.com","baidu","width=200px,height=200px,left=200px,top=200px");
//关闭窗口
newWindow.close();

var bool = confirm("确认删除吗?");
console.log(bool);

var name = prompt("请输入您的姓名","张三");
console.log(name);

//只执行一次
var timeoutId = setTimeout(function(){
    console.log("boom");
},2000);
//取消timeout
clearTimeout(timeoutId);

var i = 10;
var id = setInterval(function(){
    i--;
    console.log(i);
},1000);

clearInterval(id);
```



## document

> Document 对象使我们可以从脚本中对 HTML 页面中的所有元素进行访问。

### 方法

|                        |                                        |
| ---------------------- | -------------------------------------- |
| getElementById()       | 返回对拥有指定 id 的第一个对象的引用。 |
| getElementsByName()    | 返回带有指定名称的对象集合             |
| getElementsByTagName() | 返回带有指定标签名的对象集合           |

## date

1. getFullYear()：年
2. getMonth()：月
3. getDate()：日
4. getHours()：时
5. getMinutes():分
6. getSecinds():秒
7. getDay():星期:0-6

```javascript
var date = new Date();

var year = date.getYear();
var year1 = date.getFullYear();  //完整年份
var mon = date.getMonth();   //月份  0-11
var dayofweek = date.getDay();  //星期  0-6
var day = date.getDate();
var hour = date.getHours();
var min = date.getMinutes();
var sec = date.getSeconds();

var str = year1+"-"+(mon+1)+"-"+day+" "+hour+":"+min+":"+sec;
//console.log(str);
document.getElementById("div1").innerHTML = str;
```



## Location:

    封装当前的url的信息。

# dom操作

##  获取节点

```javascript
//通过id值获取元素节点			
var oDiv = document.getElementById("div1");	
//console.log(oDiv);	

var aDiv = document.getElementsByTagName("div");			//console.log(aDiv[0]);	

//通过class值获取元素			document.getElementsByClassName("");		

//通过name属性值获取			
document.getElementsByName();
```



 

##  获取/修改元素节点文本

>  innerHTML:获取修改文本内容
>
> innerText:获取修改文本内容(只当做普通文本处理，不能设置标签)

```javascript
//获取文本内容
var val = oDiv.innerHTML;console.log(val);
//设置内容
oDiv.innerHTML="哈哈";	

var val2 = oDiv.innerText;console.log(val2);
oDiv.innerText="aa";

 
```



##  获取/修改元素节点属性

> 节点对象.attrName=值 
>
> Class特殊:  oDiv.className="green";

 

##  修改元素的样式

```javascript
节点对象.style.样式=值

font-size--->fontSize
oDiv.style.fontSize="20px";
```





# 事件

## ui事件

1. load事件:加载事件

   > window.onload:页面加载事件,窗口的所有节点绘制完毕出发事件

2. scroll事件:滚动事件(document.onscroll)

## 鼠标事件

1. onclick:鼠标单击

2. ondblclick:鼠标双击,较短的事件点击二次,会调用单击事件
3. mouseover:鼠标的移入事件
4. mouseout:鼠标的移出事件

## 键盘事件

> 特点: 生效的前提,添加的事件在哪个节点焦点在该节点的时候时生效

1. keyup:键抬起的时候触发

   > 文本框写入后给从服务器,用keyup,keydown 返回的是上一个

2. keydown:键按下的时候触发

   > document.keydown=function(){
   >
   > 	if
   >
   > }

3. onkeypress: 可打印字符时候触发,shift等功能字符不触发

> 回车的keycode=13

```JavaScript
document.getElementById("input0").onkeyup = function(ev) {
    if(ev.keyCode == 13) {
        console.log("enter");
    }
    console.log(ev.key);
}
```



# 正则表达式

1. str.match(regex)

   > 为null的时候,没有匹配上

2. regex.test(str)

   > true: 匹配
   >
   > false:不匹配

## 规则

> /开始       /结束
>
> 开始  ^
>
> 结束 $

1. \d 代表数字:[0-9};

2. \w 代表数字字母下划线 [0-9a-zA-Z_]

3. . : 除了换行符之外的任意字符

4. {m}:出现m次

5. {m,}:出现至少m次

6. {m,n}:m-n次

7. ? :0-1次

8. \+  : 1-多次

9. \* : 0-多次

10. [\u4e00 - \u9fa5]:汉字

# json

> 本质:就是字符串,轻量级数据交互的格式
>
> 	xml:可扩展的标记语言,
>		
> 		优点:格式严格
>		
> 		缺点:无用标签多
>		
> 	json:**键值对轻量级传输**
>		
> 		主流平台支持json:
>		
> 		js对象格式的字符串:
>		
> 		**key值必须有引号**
>		
> 		java <--> json:fastjson(alibaba) / gson(google) / JSONArray / JSONobject
>
>

## JS对象

> 键值对,用:冒号连接, 用逗号,分隔多个属性
>
> 属性值key可以不使用引号

```javascript
<script>
    var jsonObj = {name:'zs' ,age:12};
	var person=[{name:'zs' ,age:12}];
	console.log(jsonObj);
	console.log(jsonObj.name);
	console.log(jsonObj.age);
	console.log(person[0].age);
</script>
```



## 区别

1. - js是一个对象

   - json :字符串

2. - js对象不要引号
   - json的key必须用引号

3. - js对象不能传输
   - json用于传输

## json和js对象的转化

1.  js对象-->json 

   > JSON.stringify(jsonObj);

   ```JavaScript
   var jsonObj = {name:'zs' ,age:12};
   var json2 =JSON.stringify(jsonObj);
   console.log(json2);
   ```

2. json --> js对象

   > JSON.parse(json);
   >
   > eval("("+json2+")");

   ```javascript
   var jso=JSON.parse(json2);
   console.log(jso);
   
   var jso2 = eval("("+json2+")");
   console.log(jso2);
   ```



# jQuery

> 原生JavaScript的函数库
>
> 使得HTML文档遍历和操作，事件处理，动画和Ajax更加简单

## 为啥使用?

1. 对原生js封装,操作更加简单
2. 对浏览器兼用很好处理
3. 丰富的插件

## 下载和安装

1. 下载:http://jquery.com/download/
2. 生产环境:[compressed, production jQuery 3.3.1](https://code.jquery.com/jquery-3.3.1.slim.min.js)
3. 开发环境,有空格回车:[uncompressed, development jQuery 3.3.1](https://code.jquery.com/jquery-3.3.1.slim.js)

### 安装

1. js函数库的下载到本地

   1. 生产环境:.min.js
   2. 开发环境:js

   ```javascript
   <script type="text/javascript" src="src/jquery-3.3.1.slim.min.js" ></script>
   ```

2. CDN: 内容分发网络

```javascript
<script type="text/javascript" src="https://cdnjs.cloudflare.com/ajax/libs/jquery/3.3.1/core.js" ></script>
```

## 使用

### 基本语法

>  $(selector).action()
>
> $: jQuery的核心(jQuery 对象)
>
> selector:选择器
>
> action :对象

### jQuery对象和dom对象的区别

1. dom---> jQuery对象

   > $(dom)

2. jQuery --->dom对象

   > 1. jQuery[0]
   > 2. jQuery.get(0)

### 选择器

#### 基本选择器

1. id选择器

   > $("#id").action()

2. 类选择器

   > $(".class").action()

3. 标签选择器：

   > $("tag")

4. 通配选择器

   > $("*").action

#### 组合选择器

1. 并集

   > $("sel1,sel2").action

2. 交集

   > $("sel1sel2").action

3. 后代

   > $("sel1 sel2").action

### 属性

> $("[属性]")

#### 筛选器



>  基本筛选器 : 关键字

>  获取节点的关键字

1. :first
2. :last
3. :eq   =
4. :lt:<
5. :gt:>
6. :odd:奇数行
7. :even:偶数行

```JavaScript
<ul>
    <li>1</li>
    <li>2</li>
    <li>3</li>
    <li>4</li>
    <li>5</li>
    <li>6</li>
    <li>7</li>	
	<li>8</li>	
    <li>9</li>
</ul>

<script>
    console.log($("li:first"));
    console.log($("li:last"));
    console.log($("li:eq(1)"));
    console.log($("li:lt(3)"));
    console.log($("li:gt(3)"));
    console.log($("li:odd"));
    console.log($("li:even"));

</script>
```



> 表单选择器

1. :text
2. :password
3. :radio等

### 操作

#### 内容

> 1. text():获取或修改内容
>
> 2. html(): 获取或修改内容

#### 属性

> 1. attr(属性名, 属性值),一个查看,二个修改
> 2. prop(属性名,属性值)
> 3. value: val()

#### 样式

> 1. css(样式的名称,样式值)

#### 遍历

> 1. jQuery对象.eq(i)
> 2. each()
>    1. $(this).attr

```JavaScript
var aLi = $("li");

//遍历li标签,修改index属性为内容
aLi.each(function(){
    $(this).attr("index",$(this).html());
});
```


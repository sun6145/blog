---
title: javaeeday06
tags:
  - null
  - null
date: 2018-08-30 09:01:08
categories:
top:
---



# html介绍

1. 超文本标记语言(hyper text markup language)
   1.  展示的信息超过了文本,不仅仅是文本,还可以是音频,视频等.
   2. 超文本: 展示的内容更加丰富(文本,音频,图像,视频,链接等)
   3. 标记语言:html写的是标记

2. 网页:  html文档相当于网页

3. html文档: 标签和文本内容组成

   1. 写html 文档就是在写标签

<!-- more -->

		1991年/1999年	4.0 版本
		2009年 5.0版本 	H5
  1. audio
  2. video
  3. canvas
  4. 语义化布局标签

# html的作用

## 软件开发架构

1. B/S:		浏览器/服务器
   1. http:超文本传输协议(公开协议),数据明文传输,用于html资源传输
   2. https:加密传输协议:https=http+ssl证书
		 C/S:	客户端/服务器
   1. 优点:
      1. 安全性略高
      2. 本地缓存数据,效率高
   2. 缺点:
      1. 占据存储
      2. 碎片化,维护成本高

## B/S开发必备条件

1. 协议:http/https
2. url:统一资源定位符(路径)
3. 资源文件: html,图片,视频等

# html标签及规范

> 标签: 一对尖括号 和 关键字组成:`<html>`

## 书写规范

1. 标签一般成对存在, 开始标签 和 结束标签如:`<html> 内容</html>`

2. 标签允许正确嵌套,但是有且只有一个根标签`<html>`

   ```html
   <html> 
       <head></head>
   </html>
   
   标签允许嵌套
   <html>是html语言的根标签,只能有一个
   ```

3. 开始标签内部可以写属性,属性值必须使用双引号

   ```html
   <html>
       <p align = "center">hello html</p>
   </html>
   ```

4. 标签不区分大小写,但是建议使用小写

5. 允许存在不成对的标签,比如空标签(没有属性的)

   ```html
   <br />
   <hr />
   <input />: 不是空标签
   ```


# html的基本格式

## 编写html文档

1. 编写一个以htm/html结尾的文件

2. 编写

   ```html
   <!DOCTYPE html>
   <html>
   	<!--注释:编码:样式,js,元数据信息等-->
   	<head>
   		<!-- 文档标题信息,收藏夹默认名称,-->
   		<meta charset="utf-8" />
   		<title></title>
   	</head>
   	
       <body>
   	
       </body>
   
   </html>
   ```


# html常见标签

## 标题`<h*>`

> h1 ~ h6 : **黑体,加粗,换行**的效果,
>
> 起强调作用
>
> 	用户: 醒目
>		
> 	搜索引擎: 添加索引,



## 段落标签`<p>`

> 区分段落内容,**默认有换行效果**.

## 换行标签

> 空标签: `<br />`换行

## 水平线`<hr />`

1. width: 可以设置餐长度;

   ```html
   <hr width="100px" />
   ```

```html
<h1>悯农</h1>
<hr width="100px" />
<p>锄禾日当午,汗滴禾下土.</p>
谁知盘中餐,粒粒皆辛苦.<br  />
```



## 超链接`<a>`

1. href: 代表链接资源(url):

2. target: 调整链接目标的打开方式

   1. _blank:新标签页打开链接的内容
   2.  _self:(**默认**),覆盖当前的标签页
   3. _paraent: 父集窗口打开
   4. _top:顶级窗口打开

3. 网络资源

   1. 网络资源使用http协议访问

   ```html
   <!--网络资源http协议访问:别人服务器上的资源-->
   <a href="https://www.baidu.com/" target="_blank">百度</a>
   ```

4. 本地资源

   1. 相对路径

      - 以当前路径为参考,
        - 同级:直接写文件名
        - 不同级:`../`

      ```html
      <a href= "a.html">本地a.html</a>
      <a href= "html/a.html">本地a.html</a>
      <a href="../index.html"></a>
      <a href="#"></a>
      ```

   2. 绝对路径

5. 锚链接

   > `#` 代表通过id寻找
   >
   > id : 标签的唯一标识

   ```html
   <h1 id ="minnong">悯农</h1>
   <a href="#minnong">悯农</a>
   ```

## 图片`<img>`

> `<img />`
>
> 一般只设置宽高的一个

1. src: 设置图片的url;

   - 本地

     ```html
     
     ​```html
     <img src="img/478.jpg" width="300px" alt="图片" title="图片" />
     ​```
     ```

   - 网络

     ```html
     <img src="http://pic19.nipic.com/20120328/5304880_152355292000_2.jpg" width="150px"/>
     
     ```

2. width:设置图片宽

3. height:设置图片的高

4. alt: 图片**无法正确显示**时,用于**提示**

5. title: **鼠标悬停**时,用于显示

## 列表标签

### 无序列表`<ul>`

> 菜单栏

1. ul:无序列表
2. li:列表项

```html
<ul>
    <li>1</li>
    <li>2</li>
    <li>3</li>
    <li>4</li>
</ul>
```

### 有序列表`<ol>`

> top榜

1. ol: 有序列表
2. li: 列表项

```html
<ol>
    <li>1</li>
    <li>2</li>
    <li>3</li>
    <li>4</li>
</ol>
```



### 自定义列表

> 直接显示列表项

1. dl:自定义列表
2. dt:列表项
3. dd:不是dt的子标签,与dt同级,**对dt进行解释**,有**缩进效果**.

```html
<dl>
    <dt>123</dt>
    <dd>456</dd>
    <dt>qwe</dt>
    <dt>asd</dt>
</dl>
```

## 表格

> `<table>`:表格的根标签
>
> 	thead:表头,**加粗效果**`<th>`
>		
> 	tbody:表体,正文信息
>		
> 	tfoot:表脚 ,统计信息
>
> 不声明thead,tfoot:默认放入到tbody中.

1. border: 边框
2. cellspacing:单元格的距离
3. cellpadding:单元格的内边距(内容和边框的距离):框变大.

### tr: 行

1. bgcolor: 一行的颜色

### td: 列

1. colspan: 合并行
2. rowspan:合并列
3. aling: 默认left/ center/ right

```html
<tbody>
    <tr>
        <td>第一列</td>
        <td colspan="2">第二列</td>
    </tr>
    <tr>
        <td rowspan="2">第一列</td>
        <td>第二列</td>

        <td>第三列</td>
    </tr>
    <tr>
        <td>第二列</td>
        <td>第三列</td>
    </tr>
<tfoot>
    <tr>
        <td>表脚</td>
        <td>表脚</td>
        <td>表脚</td>
    </tr>
</tfoot>
</tbody>
```



## 表单标签`<form>`

> http请求:
>
> 1. 组成:
>    1. 请求行 :请求路径 协议
>    2. 请求体 :key/value(页面内容类型,编码,长度等)
>    3. 请求体(可选的): 用户发送的数据
> 2. get请求:没有请求体;传输参数在url后
>    1. ?user=zs & password =123
>    2. get较小数据(1k)
>    3. 不能使用在上传情况下
> 3. post请求:参数在请求体中

1. action : 动作(服务器的url)
2. method:提交方式

### 表单项`<input />`

#### type

> 控制显示的格式

1. text: 文本输入框

2. password: 密码框

3. button: 按钮

4. submit:拥有提交表单权限的按钮

5. radio:单选按钮

   1. 作为一组的时候:**name值要一致**;
   2. 默认选中:出现checked属性
      1. checked="checked"
      2. checked=""
      3. checked

6. checkbox:多选框

   1. 作为一组的时候:**name值要一致**;

7. select:写key(name)

   1. option:写值(value)
      1. value不写,值为选中的文本内容

8. hidden:隐藏域

   > 不会显示在页面上,但是会传输

9. file:文件上传

10. reste:重置

11. `<textarea>`

    1. rows:行
    2. cols:列

#### value 

> 控制值

#### name

> 相当于key

```html
<!--get表单-->
<form action="a.html" method="get">
    账户名:<input type="text" name="username" /> <br />
    密 码:<input type="password" name="password" /><br />
    性别:<input type="radio" name="gender"  value="men" checked="checked"/> 男
    <input type="radio" name="gender" value="women"/> 女   <br />
    爱好:
    <input type="checkbox" name="hobby" value="sleep" checked="checked"/> 睡觉
    <input type="checkbox" name="hobby" value="eat"/> 吃饭
    <input type="checkbox" name="hobby" value="study"/> 学习

    <br />

    省份:
    <select name="province">
        <option value="bj">北京</option>
        <option value="sh">上海</option>
        <option value="cd">成都</option>
    </select><br />


    <!--隐藏域-->	
    <input type="hidden" name="code" value="1"/><br />

    <!--文件上传-->
    <input type="file" name="filename" /><br />

    <!--多行文本-->
    <textarea name="comment" rows="10" >请在此处输入文本...</textarea> <br />

    <input type="submit" value="提交" />
</form>
```

# 其他标签

1. div(块级标签)

   > 默认样式:和父级容器宽度褒词一致,高度默认为0
   >
   > 块级元素:默认不和其他元素共享一行
   >
   > div+scc用于布局页面

2. span(内联元素)

   > 用于标记
   >
   > 内联元素:可以嵌入到其他标签中;能够和其他标签共享一行
   >
   > 元素:从标签的开始和内容到结束标签

# 实体

> html中添加空格的时候,第一个有效果
>
> 超过一个都当作只有一个空格.
>
> **实体名称对大小写敏感！**

| 显示结果 | 描述              | 实体名称            | 实体编号  |
| -------- | ----------------- | ------------------- | --------- |
| ` `      | 空格              | `&nbsp;`            | `&#160;`  |
| <        | 小于号            | `&lt;`              | `&#60;`   |
| >        | 大于号            | `&gt;`              | `&#62;`   |
| &        | 和号              | `&amp;`             | `&#38;`   |
| "        | 引号              | `&quot;`            | `&#34;`   |
| '        | 撇号              | `&apos; (IE不支持)` | `&#39;`   |
| ￠       | 分（cent）        | `&cent;`            | `&#162;`  |
| £        | 镑（pound）       | `&pound;`           | `&#163;`  |
| ¥        | 元（yen）         | `&yen;`             | `&#165;`  |
| €        | 欧元（euro）      | `&euro;`            | `&#8364;` |
| §        | 小节              | `&sect;`            | `&#167;`  |
| ©        | 版权（copyright） | `&copy;`            | `&#169;`  |
| ®        | 注册商标          | `&reg;`             | `&#174;`  |
| ™        | 商标              | `&trade;`           | `&#8482;` |
| ×        | 乘号              | `&times;`           | `&#215;`  |
| ÷        | 除号              | `&divide;`          | `&#247;`  |
---
title: javaeeday07
tags:
  - null
  - null
date: 2018-08-31 09:02:07
categories:
top:
---



# 什么是CSS

>  css : Cascading Style Sheets 层叠样式表
>
> 给html添加样式



# css的引入方式

## css 的优先级

> 行间样式> 非行间样式
>
> 其他的**就近原则**

<!-- more -->

## 内联样式

> style属性
>
> (在 HTML 元素内部） 优先级最高

```html
<p style="color: red;font-size: 30px;"> 这是一个段落</p>
```

## 内部样式

> style标签
>
> 位于 `<head> `标签内部

```css
<style>
    p {
        color: red;
        font-size: 30px;
    }
</style>
```



## 外部样式

>  文档和样式分离
>
>  位于 `<head> `标签内部
>
>  `<link rel="stylesheet" href="css文件位置"> `
>
>  推荐使用

```css
<link rel="stylesheet" href="../css/css01.css" />

-----css01.css-------------
P{
	font-size: 30px;
	color: gold
}
```



# css的基本语法

> 声明> 样式名称: 样式值;
>
> 单个单词直接书写,多个单词或者汉字使用分号引起来

- 颜色
  1. 单词
  2. 6位16进制数据表示颜色(二位相等时可以使3位)
     1. #000: 黑色 # fff:白色
  3. RGB

```css
<style type="text/css">
    p{
        color: #008000;
        font-family: "楷体";
    }
</style>
```



# css的选择器

1. 标签选择器

   > 标签{ 样式: 值 ;....}

```css
<style type="text/css">
    h1 {
        color: #0f0;
    }
</style>
```

2. id选择器

   > 不能复用

   ```css
   <p id="p1">段落</p>
   
   <style type="text/css">
       #p1 {
           color: cyan;
       }
   </style>
   ```

3.  类选择器

    ```css
    <p class="p3">段落</p>

    <style>
        .p3 {
            color: steelblue;
        }
    </style>
	```



4. 通配选择器

   > `*` :指页面中所有的元素

   ```css
   <style>
       * {
           margin: 0;
       }
   </style>
   ```

5.  组合选择器

   1. 并集选择器

      ```css
      h2,.p1{
          color: green;
      }
      
      h2
      class='p1'
      的标签都会改变
      ```

   2. 交集选择器

      ```css
      h2.red{
          color:red;
      }
      
      <h2 class="red">标题2</h2>
      ```

   3.  后代选择器

      > 空格分开
    
      ```css
      p span{
          color: red;
      }
      <p> 白日依山尽 ,<span>黄河</span>入海流. </p>
      ```

   4.  属性选择器

      > 标签中只有一个有特有属性:[]属性名]{}
      >
      > 多个: [属性名="值"]
    
      ```css
      <style>
          [name]{
              border:;
          }
      </style>
      
      <input type="text" name="username" />
      <input type="password" />
      ```
    
      ```css
      [name="pwd"]{
          border:1px solid red;
      }


      
      <input type="text" name="username" />
      <input type="password"   name="pwd" />
      ```




# 常见的css样式

## 文本样式

1. 颜色: **color**
2. 文本对齐方式: **text-align**:(水平方向)
3. line-height:行高

```css
color:gray;
text-align: center;
line-height: 30px;
```

## 文字样式

1. font-size: 字体大小;
   1. 具体像素
   2. 百分比(对比父级)

2. font-family:字体类型(不同类型,逗号隔开)
   1. 先英文
   2. 后中文

3. font-weight:字体的权重
   1. normal   :　 默认值。正常的字体。相当于 400 。声明此值将取消之前任何设置 
   2. bold     :　 粗体。相当于 700 。也相当于 b 对象的作用 
   3. bolder   :　 比 normal 粗 
   4. lighter  :　 比 normal 细 
   5. 100-900

## 宽高样式

1. width: 宽

2. height:高

   1. px
   2. %

   > 使用百分比需要确定父级的宽高

## 背景样式

1. background-color:背景颜色;

2. background-image:背景图片

3. background-repeat:背景重复

   > repeat     :　 默认值。背景图像在纵向和横向上平铺 
   > no-repeat  :　 背景图像不平铺 
   > repeat-x   :　 背景图像仅在横向上平铺 
   > repeat-y   :　 背景图像仅在纵向上平铺 

4. background-position:背景定位

```css
background-color: aliceblue;
background-image: url(../img/cj0.jpg);
background-repeat: no-repeat;
background-position: 100px, 100px;
```



## 列表样式

1. list-style-type: 列表前的图形
   1. none;

```css
ul li{
    list-style-type: none;
}
<ul>
    <li>1</li>
    <li>2</li>
</ul>
```

## 浮动样式

> 给一个标签添加浮动后,会脱离标准文档流,
>
> left浮动:挨着父级容器的左边框或者已经浮动的容器的右边框停止浮动
>
> 子元素的浮动之和不能超过父级的大小



# 盒子模型

> 夏敏属性都分为上下左右四部分
>
> 可以简写:
>
> 	4个:顺时针 上 右 下 左
>		
> 	2个: 上下   左右
>		
> 	1个: 上下左右

## 边框(border)



## 内边距(padding)

> 边框和真实内容的间距





# 居中

margin:0 auto；在不同场景下生效条件如下：

        **块级元素**：给定要居中的块级元素的宽度。
    
        **行内元素**：①设置display:block；②给定要居中的行内元素的宽度。（行内元素设置成块级元素后可以对其宽高进行设置）
    
        **行内块元素**：设置display:block。（如input、button、img等元素，自带宽度可以不用设置其宽度）



# JavaScript 

> 弱类型 动态类型 基于对象(原型) 的直译性变成语言
>
> 类型用var表示所有类型

## 函数

### 定义

```javascript
// 无参无返回值
function func(){
	console.log(1);//控制台输出
}

//调用
func();
```

```javascript
//带参数
//1. 参数不用写类型
//2. 个数可以不一致，按顺序取，多的不用
function func2(a,b){
    console.log(a+b);
}
```

```javascript
//带返回值
//return

function func3(a,b){
    return a+b;
}


//使用
var c = func3(1,20);
//console.log(c);
```



## 事件调用方法

1. onclick();
---
title: jQuery
tags:
  - null
  - null
date: 2018-09-05 09:07:40
categories:
top:
---



# JQuery

## 样式的修改

1. css("样式名" , "属性值")
2. addClass("类选择器名")
3. removeClass():删除类属性

<!-- more -->

```javascript

.red {
    width: 200px;
    height: 200px;
    background: red;
}

.green {
    width: 300px;
    height: 500px;
    background: green;
}
<div> </div>

$("div").addClass("green");
$("div").removeClass("green")
$("div").addClass("red");
```

## 事件

> 与原生的大致相同,不用on

```javascript
<div onclick="togreen()"></div>

function togreen(){
    $("div").css("background","green");
}

$("div").click(function(){
    $(this).css("background","green");
});

$("div").mouseover(function(){
    $(this).css("background","pink");
});
$("div").mouseout(function(){
    $(this).css("background","red");
});
```

### onload事件

1. onload():  -----> $(document).ready(fun());-----> 简写$();

```javascript
<script>
    $(function(){
    console.log($("div"))
});
</script>
```



### 事件的绑定

1. on("事件类型,事件类型", 函数);
2. off("事件类型")



<div onmouseover=""></div>

```javascript
<script>
    $("div").on("click mouseover", function() {
    //				$(this).css("background", "blue");
  	  console.log("123");
	});

	$("div").off("mouseover");

</scrip>
```
# 服务器

> 1. 性能强大的计算机(硬件)
> 2. 操作系统(windows/Linux)
> 3. 服务器软件(部署资源供外部访问)
> 4. web应用程序(处理 浏览器端请求)

## 服务器软件

1. IIS 服务器:Microsoft 大型服务软件
2. weblogic:oracle服务器软件(支持13种动态网页开发技术)
3. webphere: IBM大型服务器软件
4. apache: apache,开源软件基金会,开源
5. nginx: web服务器/反向代理服务器(分布式)
6. Tomcat: Apache下的项目,开源,免费的web应用服务器

## Tomcat

> 开源,免费web服务器 ,主要有Apache ,sun 以及其他进行开发和维护
>
> 需要jdk环境支持,能兼容最新的jdk.

### 下载

1. 下载:https://tomcat.apache.org/download-80.cgi

2. http://archive.apache.org/dist/

### 安装

1. 解压
2. 目录
   1. bin:可执行文件(启动startup,关闭shutdown Tomcat)
   2. conf:配置文件
   3. lib: 类库,依赖的jar
   4. log.tomcat :日志信息
   5. temp:临时文件
   6. webapps: 项目目录
   7. work: 工作的目录jsp页面处理,
3. 检验:http://127.0.0.1:8080/

# web应用程序

1. eclipse集成Tomcat

   1. 配置server环境
   2. 创建Tomcat服务器
   3. 配置Tomcat的目录和映射位置(servre locations use tomcat installation  ,deploy path: webapps)

2. 创建dynamic web project

   1. context root: 项目在Tomcat中的根目录(webapps下的目录名称)
   2. content directory:放置所有的资源都在该目录底下,应用程序中的目录(在磁盘中不真实存在) 对应root

3. WebContent:资源文件的根目录

   1. WEB-INF:该目录下的所有资源**不能被外部直接访问**
      -  lib:存放jar包
      - web.xml:应用程序配置文件

4. xml:可扩展的标记语言

   1. 声明:`<?xml version="1.0" encoding="UTF-8"?>`

      ```xml
      <!-- 约束(dtd/schema) 
           xmlns: xml的命名空间(规定标签)
           schemaLocation: 约束文件的地址
           .xsd:schema约束的路径
           .dtd:dtd文件的约束路径
      -->
      ```

   2. `<welcome-file-list>`

      ```xml
      <!--  欢迎文件列表(首页) -->
      <welcome-file-list>
          <welcome-file>index.html</welcome-file>
          <welcome-file>index.htm</welcome-file>
          <welcome-file>index.jsp</welcome-file>
          <welcome-file>default.html</welcome-file>
          <welcome-file>default.htm</welcome-file>
          <welcome-file>default.jsp</welcome-file>
      </welcome-file-list>
      ```


# servlet

> 运行于**服务器端**应用程序,必须实现servlet借口
>
> 接受和处理用户请求(http);对客户端进行相应

## 开发servlet

1. 创建servlet类,实现servlet接口-->继承HttpServlet(javax.servlet.http.HttpServlet;)

2. 配置servlet路径

   1. `<servlet>`
      1. ` <servlet-name>`
      2. ` <servlet-class>`
   2. `<servlet-mapping>`
      1. ` <servlet-name>`
      2. 访问规则:``

   ```xml
   <servlet>
       <!-- 名称 -->
       <servlet-name>user</servlet-name>
       <!-- 类的全限定名称 -->
       <servlet-class>servlet.UserServlet</servlet-class>
   </servlet>
   
   <!-- servlet的映射(servlet对象和URL之间的映射) -->
   <servlet-mapping>
       <servlet-name>user</servlet-name>
       <!-- 访问规则: 
   	/路径: http://localhost:8080/项目根路径/路径 
   	/*  : http://localhost:8080/项目根路径/任意路径 
       *.do: http://localhost:8080/项目根路径/任意路径.do 
   -->
       <url-pattern>*.do</url-pattern>
   </servlet-mapping>
   
   
   ```

3. 测试

    * http://localhost:8080/javaweb01/user
    * http://localhost:8080/javaweb01/index.html

   ```html
   <form action="user.do" method="post">
       <label>用户名:</label>
       <input type="text" name="username">
       <br />
       <label>密码:</label>
       <input type="password" name="password">
       <br />
       <input type="submit" value="提交">
   </form>
   ```

# 状态码

1. 200:请求成功
2. 302:重定向
3. 4XXX:客户端的问题
   1. 404:文件没有找到
   2. 405:客户端异常
4. 5XXX: 服务器异常
   1. 502:
   2. 503

# HttpServletRequest

> 1. 请求对象(请求行,请求头,请求体)
>    1. get: 请求行
>    2. post:请求体

1. getParameter(name);获取名字获取提交的信息
2. getRemoteAddr();客户端的ip
3. getRemotePort();客户端的端口
4. getMethod:获取请求的方式

# HttpServletResponse

> 响应对象

```java
//流
PrintWriter writer = resp.getWriter();
writer.println("success");
writer.close();
```

1. 页面内容较多时,使用printwrite不方便,

   1. 解决:jsp

2. 乱码问题:

   1. 原因:编码不一致

   2. 解决:

      > 请求

      1. 统一使用utf-8;

      2. get:修改tomcat服务器tomcat (7之前)的conf/server.xml-->URIEncoding

         ```
         <Connector connectionTimeout="20000" port="8080" protocol="HTTP/1.1" redirectPort="8443"  URIEncoding="UTF-8"/>
         ```

      3. post:req.setCharacterEncoding("utf-8");

      > 响应

      1. resp.setCharacterEncoding("utf-8");

      2. 浏览器需要以响应的编码的格式显示

         ```
         //设置响应头信息
         //本质不是设置编码的
         //服务器给客户端响应的数据类型
         resp.setContentType("text/html;charset=utf-8");
         ```

3. get请求,没有乱码,

   1. 8.0之后,tomcat 默认编码改为了utf-8(7--> lanter1)

4. post请求,出现乱码

# 域

> 实现数据的共享

## request域

> HttpServletRequest req; 作为域对象的时候,共享数据

1. req.setAttribute(name, value);
2. req.getAttribute(name);
3. req.removeAttribute(name);

### 作用域:一次请求内有效



# jsp

> jsp形式:html+java
>
> 本质:servlet
>
> eclipse编写jsp:preferences;修改编码

1. 请求index.jsp页面
2. 服务器翻译为index.jsp.java
   1. 将jsp中的标签通过响应的流的write方法写出.
3. 编译index.jsp.class文件

> java代码的编写需要用`<%` java代码`%>`
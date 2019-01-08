---
title: javaeday10JSP
tags:
  - null
  - null
date: 2018-09-06 09:33:55
categories:
top:
---

<!-- more -->

# 转发和重定向

## 转发

> 服务器端的行为
>
> `request.getRequestDispatcher("URL").forward(request, response);`

1. 客户端只发送一次请求
2. 服务器端将该请求在内部实现共享(request,response)



## 重定向

> 客户端的 行为
>
> `response.sendRedirect("show.jsp");`

1. 客户端发送第一次请求,
2. 服务器返回302状态码和新的请求路径(location);
3. 客户端拿到该数据,向心得路径发送第二次请求

# ServletContext

> application 域对象,作用域
>
> 整个应用程序有效,共享的数据(网站的在线人数)

> 不建议存放过多数据,

1. getServletContext();
2. setAttribute(name, object);
3. getAttribute(name);
4. removeAttribute(name);

```java
//创建ServletContentext数据
ServletContext servletContext = getServletContext();
servletContext.setAttribute(name, object);
servletContext.getAttribute(name);
servletContext.removeAttribute(name);
```

# 会话

>  会话可以代表与服务器端的一次连接。
>
> 通过三次握手域服务器建立连接，此时会话开始；
>
> 会话超时或者主动断开连接此时会话失效。
>
> 一次会话之内可以有多次请求和响应。

## Cookie 和session

会话跟踪技术:

## cookie:

> 一段保存于客户端的文本
>
> 服务器接收到请求后,创建cookie,返回给客户端

> **默认浏览器不关闭,,cookie有效**
>
> 客户端每一次请求都会携带cookie信息
>
> 不安全,隐私度不高的数据

`Cookie cookie = new Cookie(name, value);`

`response.addCookie(cookie);`

1. 有效时长:

   1. 默认: 关闭浏览器,cookie失效

   2. 手动设置: 

      cookie.setMaxAge(3600\*24\*365);//单位秒

3. 获取:从request请求中获取
   1. request.getCookies(); // cookie不止一个,获取全部cookie
   2. 遍历:
      1. getName()
      2. getValue()

```java
Cookie cookie = new Cookie("username", username);
response.addCookie(cookie);
cookie.setMaxAge(30);
Cookie[] cookies = request.getCookies();
for (Cookie c : cookies) {
    if ("user".equals(c.getName())) {
        System.out.println(c.getValue());
    }
}
```

## session

> (保存用户信息,实现登录验证)

> session保存于服务器,返回seesionID(自动生成)
>
> session 的标识: sessionId;(存放于cookie)
>
> 每一个用户单独拥有一个session
>
> 关闭浏览器session不会失效,但是sessionid会丢失

> session :也是一个域对象

1. 创建

   `HttpSession session = request.getSession();`

2. 保存

   `session.setAttribute("username", username);`

3. 获取

   `session.getAttribute("username");`

4. 登录验证:

   > 正常登录得到session
   >
   > 不正常时候,没有session
   >
   > 判断session为空,去登录页面

### 失效

1. 默认: 30分钟

2. 手动设置:

   `	session.setMaxInactiveInterval(3600);`//单位秒

3. 注销:立即失效

   `invalidate();`

# EL表达式和JSTL

> 使用EL表达式获取数据语法："**${标识符}**"
>
> 　EL表达式语句在执行时，会调用pageContext.findAttribute方法，用标识符为关键字，分别从page、request、session、application四个域中查找相应的对象，找到则返回相应对象，找不到则返回”” （注意，不是null，而是空字符串）。

> EL表达式可以很轻松获取JavaBean的属性，或获取数组、Collection、Map类型集合的数据

1. 获取域中的数据

   ```java
   <% 
       request.setAttribute("name","孤傲苍狼");
   %>
       <%--${name}等同于pageContext.findAttribute("name") --%>
            使用EL表达式获取数据：${name}  
   ```

2. 获取bean的属性

   ```java
   <% 
       Person p = new Person();
       p.setAge(12);
       request.setAttribute("person",p);
   %>
       使用el表达式可以获取bean的属性：${person.age}
   ```

3. 获取对象中的对象的属性

   ```jsp
   <% 
   Person person = new Person();
   Address address = new Address();
   person.setAddress(address);
   
   request.setAttribute("person",person);
   %>
   
    ${person.address.name}
   ```


4. 获取list集合中指定位置的数据

   ```jsp
   <% 
       Person p1 = new Person();
       p1.setName("孤傲苍狼");
   
       Person p2 = new Person();
       p2.setName("白虎神皇");
   
       List<Person> list = new ArrayList<Person>();
       list.add(p1);
       list.add(p2);
   
       request.setAttribute("list",list);
   %>
   
   ${list[1].name} 
   ```

5. 迭代list集合

   ```jsp
   <c:forEach var="person" items="${list}">
       ${person.name}
   </c:forEach>
   ```

6. 获取map集合的数据

   ```jsp
   <% 
       Map<String,String> map = new LinkedHashMap<String,String>();
       map.put("a","aaaaxxx");
       map.put("b","bbbb");
       map.put("c","cccc");
       map.put("1","aaaa1111");
       request.setAttribute("map",map);
   %>
   
    <!-- 根据关键字取map集合的数据 -->
       ${map.c}  //cccc
       ${map["1"]}	//aaaa1111
   ```

7. 迭代map集合

## JSTL

1. 导入jstl的jar包





# 过滤器

> 过滤请求和响应

> 应用场景:
>
>  1. 全局编码设置
>
> 	2. 用户登录验证(首页,登录页面,登录处理servlet不能拦截)
>
>     	1. 将servletRequest强转为HttpServletRequest
>
>         `HttpServletRequest req =(HttpServletRequest) request;`
>
>     	2. 获取请求的链接
>
>         `String uri=req.getRequestURI();`
>
> 	3. 敏感词汇的过滤
>
> 	4. 图片上传后压缩

1.  init()方法：这是过滤器的初始化方法，在Web容器创建了过滤器实例之后将调用这个方法进行一些初始化的操作，这个方法可以读取web.xml中为过滤器定义的一些初始化参数。
2. doFilter()方法：这是过滤器的核心方法，会执行实际的过滤操作，当用户访问与过滤器关联的URL时，Web容器会先调用过滤器的doFilter方法进行过滤。
3. destory()方法：这是Web容器在销毁过滤器实例前调用的方法，主要用来释放过滤器的资源等。

4. `chain.doFilter(request, response);`放行

# 监听器

## 监听域对象自身的创建和销毁的事件监听器

监听对象(request/session/application)的创建和销毁,以及属性的变化

1. 监听application域,

   >  ServletContext,继承ServletContextListener
   >
   >  实现该接口中的contextInitialized和contextDestroyed方法,
   >
   >  启动服务器创建应用程序上下文时和关闭服务器销毁程序上下文时执行的操作。

2. 监听 Session 域,

   > HttpSession,继承HttpSessionListener
   >
   > 监听的是用户会话对象的创建和销毁事件
   >
   > 初始页面index.[jsp](https://www.2cto.com/kf/web/jsp/)，会发现控制台打印sessionCreated，关闭用户会话，这时会打印sessionDestroyed。

3. 监听request域,

   > ServletRequest,继承ServletRequestListener
   >
   > 启动项目，访问index.jsp?name=imooc，这里定义一个名为name的参数，参数值是imooc，提交请求
   >
   >
   >
   > 首先用户提交请求时，请求对象被创建，监听器监听到请求对象创建的事件，这时执行监听器的initialize方法，同时监听器获取到参数名为name的参数值并打印。因为request对象只在一次请求有效，所以服务器返回响应后请求对象便被销毁，这时执行监听器的destory方法。


## 监听域对象中属性的增加和删除的事件监听器

> 这一类监听器主要监听ServletContext、HttpSession和ServletRequest这三个域对象中属性的创建、销毁和修改的事件，要实现这三种监听器，就需要继承ServletContextAttributeListener、HttpSessionAttributeListener和ServletRequestAttributeListener这三个接口，

# Ajax

> AJAX = Asynchronous JavaScript and XML（异步的 JavaScript 和 XML）
>
> 点是在不重新加载整个页面的情况下，可以与服务器交换数据并更新部分网页内容。
>
> AJAX 不需要任何浏览器插件，但需要用户允许JavaScript在浏览器上执行。

1. 同步请求: 
   1. 传统的网页（不使用 AJAX）如果需要更新内容，必需重载整个网页面。

2. 异步请求

4.处理响应数据

|                    |                                                              |
| ------------------ | ------------------------------------------------------------ |
| onreadystatechange | 存储函数（或函数名），每当 readyState 属性改变时，就会调用该函数。 |
| readyState         | 存有 XMLHttpRequest 的状态。从 0 到 4 发生变化。             |
|                    | 0: 请求未初始化                                              |
|                    | 1: 服务器连接已建立                                          |
|                    | 2: 请求已接收                                                |
|                    | 3: 请求处理中                                                |
|                    | 4: 请求已完成，且响应已就绪                                  |
| status             | 200: "OK"                                                    |
|                    | 404: 未找到页面                                              |

		

- 0: 请求未初始化
- 1: 服务器连接已建立
- 2: 请求已接收
- 3: 请求处理中
- 4: 请求已完成，且响应已就绪

```javascript
//监听请求状态
xhr.onreadystatechange=function(){
    console.log(xhr.status);//状态码:200响应成功
    if(xhr.readyState==4&&xhr.status==200){
       
       }
}
```

## jQuery

1. $.get();
2. $.post();

```javascript
function myFunction()
{
    loadXMLDoc("/try/ajax/ajax_info.txt",function()
    {
        if (xmlhttp.readyState==4 && xmlhttp.status==200)
        {
            document.getElementById("myDiv").innerHTML=xmlhttp.responseText;
        }
    });
}
```

```javascript
$.get("ajax?key="+val,function(){
    
});
```


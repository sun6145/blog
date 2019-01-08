---
title: javaeeday13SpringMVC
tags:
  - null
  - null
date: 2018-09-11 09:29:17
categories:
top:
---

SpringMVC和ssm框架的整合

<!--more-->

# SpringMVC



## 参数的封装

> 简单数据:   url中参数名
>
>  对象:  url参数名称和对象
>
> 数组: url参数名称和数组名称一致
>
> list和map集合:不能直接封装，需要作为对象的属性存在

### 手动使用request对象

```java
	// 参数的接收
	// 手动给出request和response

	@RequestMapping("/login.do")
	public String login(HttpServletRequest request,
			HttpServletResponse response) {

		System.out.println(request.getAttribute("username"));
		return "show";

	}
```



### SpringMVC的方式

> springMVC底层已经将链接后的参数获取,我们只需要手动接收

```java
http://localhost:8080/springMVCDemo02/login.do?username=zs&pwd=123456
@RequestMapping("/login.do")
	public String login(String username, String password) {

		System.out.println(username + ":" + password);		//zs:null
        System.out.println(username + ":" + pwd);		//zs:123456
		return "show";

	}
```

### 使用对象接收

> 类对象的属性名称要和连接中的参数名称保持一致

```java
//1. 创建一个user.class,给出username和password属性
//2.给出访问器和toString方法

//访问:http://localhost:8080/springMVCDemo02/login.do?username=zs&password=123456&hobby=book&hobby=fruit

@RequestMapping("/login.do")
public String login(User user, String[] hobby) {
    System.out.println(Arrays.toString(hobby));	//[book, fruit]
    System.out.println(user);	//User [username=zs, password=123456]
    return "show";

}

```

### 封装到list/map集合

> 不能直接封装

```htmL
<form action="login.do" method="get">
    用户名:<input type="text" name="username"><br>
    密码:<input type="text" name="password"><br/>
    爱好:<input type="text" name="aaa[0]">
    <input type="text" name="aaa[1]"><br>
    备注:	<input type="text" name="map[name]">
    <input type="text" name="map[age]">
    <input type="submit" value="注册">
</form>
```



```java
//实体类
private String username;
private String password;
private List aaa;
private Map map;

@RequestMapping("/login.do")
public String login(User user, String[] hobby) {
    System.out.println(Arrays.toString(hobby));
    System.out.println(user);
    return "show";

}

//结果
User [username=aa, password=bb, aaa=[cc, dd], map={age=ff, name=ee}]
```

## 数据的回显

> Model 相当于request域

```java
@RequestMapping("/user.do")
public String login(User user, Model model) {
    System.out.println(user);
    //相当于request域
    model.addAttribute("user", user);
    return "show";

}


//show.jsp获取
<body>
	hello,${user.uname }
</body>
```

## 转发和重定向

> forward:url:转发
>
> redirect:url: 重定向



```java
@RequestMapping("/login.do")
public String login(User user, String[] hobby) {
    System.out.println(Arrays.toString(hobby));
    System.out.println(user);
    // return "show";
    // 转发
    return "forward:user.do";

    // 重定向:requsetd中的值会丢失
    // return "redirect:user.do";

}
```

## 编码问题

1. 过滤器处理编码
2. 在web.xml中配置过滤器

```xml
<filter>
    <filter-name>charset</filter-name>
    <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
    <init-param>
        <param-name>encoding</param-name>
        <param-value>utf-8</param-value>
    </init-param>
</filter>


<filter-mapping>
    <filter-name>charset</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>

```





# ssm整合

> 1. Mybatis 数据持久层
> 2. Spring 业务逻辑层(对象管理)
> 3. SpringMVC 表示层



```txt
1.需求使用ssm实现查询所有用户信息并展示。
  index.jsp 展示
  show.jsp
  1)创建web项目,导入jar
	 mybatis:
	   mysql.jar
	   mybatis.jar
	   
	   mybatis-spring.jar：spring创建管理mybatis中的对象
	   
     spring:
	   4+1
	   spring-aop.jar
	   spring-aspects.jar
	   aopalliance.jar
	   aspectjweaver.jar
	   spring-web.jar
	   spring-test.jar
	   
	   spring-jdbc.jar  (连接池)
	   spring-orm.jar  (整合orm框架)
	   spring-tx.jar   (事务管理)
	   
	 springMVC：
        spring-webmvc.jar	
        jackson-all.jar	
     其他:
        log4j.jar
		jstl.jar
  
    2)mybatis项目实现数据查询
	3)mybatis和spring整合
		3.1 连接和事务管理交给spring
		3.2 SqlsessionFactory，sqlsession，mapper对象创建交给spring
	4)整合springMVC	
	
	

```

## 整合出现的问题,

### 整合mybatis

1. 将mybatis.xml文件的上下文环境交给spring创建

```xml
<context:component-scan base-package="service"></context:component-scan>

<!-- 1.创建管理连接   四个参数 -->
<context:property-placeholder location="classpath:jdbc.properties"/>
<bean id="ds" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
    <property name="driverClassName" value="${driver}"/>
    <property name="url" value="${url}"/>
    <property name="username" value="${user}"/>
    <property name="password" value="${password}"/>
</bean>
<!-- 2.SqlsessionFactory -->
<bean id="ssf" class="org.mybatis.spring.SqlSessionFactoryBean">
    <property name="configLocation" value="classpath:mybatis.xml"></property>
    <property name="dataSource" ref="ds"></property>
</bean>
<!--3.创建mapper 两种方式:  单个mapper创建    批量创建-->
<bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
    <property name="sqlSessionFactoryBeanName" value="ssf"></property>
    <property name="basePackage" value="mapper"></property>
</bean>

```

2. 测试

   ```java
   @Autowired
   UserService service;
   
   @org.junit.Test
   public void test() {
   	System.out.println(service.queryAll());
   }
   ```


3. 测试时候出现问题,空指针

   > 原因: 创建service的时候没有加载applicationContext.xml的配置文件,依赖注入失败

   解决办法: 加载applicationContext.xml

   ```java
   @RunWith(SpringJUnit4ClassRunner.class)
   @ContextConfiguration(locations="classpath:applicationContext.xml")
   public class Test {
       /* //mybatis的使用
   	 * public void test() throws IOException {
   	 * 
   	 * // 1.加载配置文件 Reader rd = Resources.getResourceAsReader("mybatis.xml"); //
   	 * 2.创建sqlsession工厂 SqlSessionFactory factory = new
   	 * SqlSessionFactoryBuilder().build(rd);
   	 * 
   	 * // 创建session
   	 * 
   	 * SqlSession session = factory.openSession(true); UserMapper mapper =
   	 * session.getMapper(UserMapper.class); List<UserInfo> info =
   	 * mapper.queryAll(); System.out.println(info); session.close();
   	 * 
   	 * 
   	 * }
   	 */
   
       @Autowired
       UserService service;
   
       @org.junit.Test
           public void test() {
           System.out.println(service.queryAll());
       }
   }
   
   ```


### 整合SpringMVC



# 整合小节

1. mybatis是数据持久层的框架(dao)

   1. 将mybatis.xml文件的上下文环境交给spring创建

   2. mybatis.xml只用
      1. 加载jdbc的配置文件,

      2. 给实体类起别名

         1. 作用,在给mybatis.xml的映射文件中的sql标识返回值

            ```java
            <typeAliases>
            	<!-- 设置单一别名 -->
                <!-- <typeAlias type="pojo.User" alias="user" /> -->
                <!-- 批量起别名 默认的名称为类名 -->
                <package name="pojo" />
            </typeAliases>
            
            //映射文件的resultType可以用user代替
            <select id="selectDeptinfo" resultType="pojo.User">
            		select * from dept where
            		deptno =#{deptno}
            </select>
            
            ```

      3. 给实体类添加映射器` <mappers>`

         1. 可以批量添加一个包下mapper

2. Spring 调用service 层  ,service 调用mapper映射对象,需要加载applicationContext.xml

3. 编写springMVC层,建立一个后端控制器(Controller),调用service

   1. 编写前端控制器web.xml

      **注意: 我们需要指定SpringMVC.xml的路径,默认在web-INF**

      ```xml
      <servlet>
          <servlet-name>mvc</servlet-name>
          <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
          <init-param>
              <param-name>contextConfigLocation</param-name>
              <param-value>classpath:SpringMVC.xml</param-value>
          </init-param>
      </servlet>
      
      <servlet-mapping>
          <servlet-name>mvc</servlet-name>
          <url-pattern>*.do</url-pattern>
      </servlet-mapping>
      ```

   2. 编写springMVC.xml

      1. 扫描控制器(映射器,适配器)需要在头文件上添加mvc的信息,能够识别controller包下的注解

         ```
          xmlns:mvc="http://www.springframework.org/schema/mvc"
          http://www.springframework.org/schema/mvc 
          http://www.springframework.org/schema/mvc/spring-mvc.xsd"
         ```

      2. 编写视图解析器 

   **注意:出现找不到Userservice**

   ```txt
   No qualifying bean of type [service.UserService] found for dependency: expected at least 1 bean which qualifies as autowire candidate for this dependency. Dependency annotations: {@org.springframework.beans.factory.annotation.Autowired(required=true)}
   	
   ```

   > **Service是由Spring进行管理的**,**使用service对象的时候,应该先加载applicationContext.xml文件**

   ```xml
   applicationContext.xml
   <context:component-scan base-package="service"></context:component-scan>
   ```

   解决办法:**在web.xmL文件中添加监听器(保证在发请求之前就加载配置文件)**



   ```xml
   1.添加监听器,在请求之前就要加载配置文件
   
   
   //指定路径
   <context-param>
       <param-name>contextConfigLocation</param-name>
       <param-value>classpath:applicationContext.xml</param-value>
   </context-param>
   
   
   //监听的时候加载配置文件
   <listener>
       <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
   </listener>
   <servlet>
   ```

---
title: javaeeday12Spring
tags:
  - null
  - null
date: 2018-09-08 09:29:17
categories:
top:
---



# Spring

> Spring是一个开放源代码的设计层面框架，他解决的是业务逻辑层和其他各层的松耦合问题，因此它将**面向接口的编程思想**贯穿整个系统应用。
>
> Spring是一个分层的JavaSE/EE **full-stack(****一站式)** [轻量级](https://baike.baidu.com/item/%E8%BD%BB%E9%87%8F%E7%BA%A7/10002835)开源框架。
>
> 学习核心:1. IOC控制反转 ; 2. 面向切面编程 



<!-- more -->

**1.方便解耦，简化开发**

通过Spring提供的IoC容器，我们可以将对象之间的依赖关系交由Spring进行控制，避免硬编码所造成的过度程序耦合。有了Spring，用户不必再为单实例模式类、属性文件解析等这些很底层的需求编写代码，可以更专注于上层的应用。

**2.AOP**[**编程**](https://baike.baidu.com/item/%E7%BC%96%E7%A8%8B)**的支持**

通过Spring提供的[AOP](https://baike.baidu.com/item/AOP)功能，方便进行面向切面的编程，许多不容易用传统OOP实现的功能可以通过AOP轻松应付。

3.[**声明式事务**](https://baike.baidu.com/item/%E5%A3%B0%E6%98%8E%E5%BC%8F%E4%BA%8B%E5%8A%A1)**的支持**

在Spring中，我们可以从单调烦闷的事务管理代码中解脱出来，通过声明式方式灵活地进行事务的管理，提高开发效率和质量。

**4.方便程序的测试**

可以用非容器依赖的编程方式进行几乎所有的测试工作，在Spring里，测试不再是昂贵的操作，而是随手可做的事情。例如：Spring对Junit4支持，可以通过注解方便的测试Spring程序。

**5.方便集成各种优秀框架**

Spring不排斥各种优秀的开源框架，相反，Spring可以降低各种框架的使用难度，Spring提供了对各种优秀框架（如Struts,Hibernate、Hessian、Quartz）等的直接支持。

**6.降低Java EE API的使用难度**

Spring对很多难用的Java EE API（如JDBC，JavaMail，远程调用等）提供了一个薄薄的封装层，通过Spring的简易封装，这些Java EE API的使用难度大为降低。

**7.Java 源码是经典学习范例**

Spring的源码设计精妙、结构清晰、匠心独运，处处体现着大师对[Java设计模式](https://baike.baidu.com/item/Java%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F)灵活运用以及对Java技术的高深造诣。Spring框架源码无疑是Java技术的最佳实践范例。如果想在短时间内迅速提高自己的Java技术水平和应用开发水平，学习和研究Spring源码将会使你收到意想不到的效果。

![spring框架](https://www.github.com/sun6145/githubPicture/raw/master/img/spring-overview.png )





## 什么是ICO

> 控制反转,用户创建对象的权利反转给spring容器
>
> 创建对象和管理对象的关系都有spring容器操作

DL:依赖注入:在运行过程中将数据动态注入对象依赖的过程(反射实现)

[Spring IOC（控制反转）的理解](https://www.cnblogs.com/Mr-Rocker/p/7721824.html)

# 入门案例

## 开发

1. jar包(4+1)

   1. spring-core

      spring-beans

      spring-context

      spring-ecpression

      commons-logging （archive.apache.org）,mybatis中可以找到

2. 新建java程序

3. 创建dao接口和实现类

   ```java
   package dao;
   
   public interface UserDao {
   
       void addUser();
   
       void deleteUser();
   
   }
   
   
   //---实现类1------------------------------------------
   
   
   package dao.impl;
   
   import dao.UserDao;
   
   public class UserDaoImpl implements UserDao {
   
       @Override
       public void addUser() {
           System.out.println("添加用户");
       }
   
       @Override
       public void deleteUser() {
           System.out.println("删除用户");
       }
   
   }
   
   
   
   //---实现类2------------------------------------------
   
   package dao.impl;
   
   import dao.UserDao;
   
   public class UserDaoImpl2 implements UserDao {
   
       @Override
       public void addUser() {
           System.out.println("添加用户2");
       }
   
       @Override
       public void deleteUser() {
           System.out.println("删除用户2");
       }
   
   }
   ```

4. 创建service接口和实现类

   ```java
   package service;
   
   public interface UserService {
   
       void addUser();
   
       void deleteUser();
   
   }
   
   
   //---实现类1------------------------------------------
   package service.impl;
   
   import dao.UserDao;
   import dao.impl.UserDaoImpl2;
   import service.UserService;
   
   public class UserServiceImpl implements UserService {
   
       @Override
       public void addUser() {
           dao.addUser();
       }
   
   
       @Override
       public void deleteUser() {
           dao.deleteUser();
       }
   
   }
   
   ```

5. 新建source  fFolder,编写**applicationContext.xml**

   > - 通过配置文件来创建对象,一个`<bean>`为一个对象 
   >
   > 	id是对象的唯一标识
   >
   > 	class:**实现类**的全限定名称
   >
   > - 依赖注入dl
   >
   >   - **接口+setter方法**
   >
   >     配置文件调用构造方法给属性赋值`<property>`
   >
   >     name:对象的属性名
   >
   >     value/ref:基本类型/引用类型()
   >
   >   - **接口+有参构造:**
   >
   >     `<constructor-arg>`
   >

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="
    	   http://www.springframework.org/schema/beans 
    	   http://www.springframework.org/schema/beans/spring-beans.xsd">
   
       <!-- 创建一个对象   id:对象唯一标识     class:类的全限定名称 -->
       <bean id="userdao" class="dao.impl.UserDaoImpl"></bean>
   
       <bean id="userdao2" class="dao.impl.UserDaoImpl2"></bean>
   
       <!-- 调用无参构造创建对象 -->
       <bean id="userservice" class="service.impl.UserServiceImpl">
           属性标签:  name:属性的名称   value:简单类型的值    ref:引用
           <property name="dao" ref="userdao2"></property>
       </bean> 
   
       <!-- 有参构造 -->
       <bean id="userservice" class="service.impl.UserServiceImpl">
           <!-- 
   			name:名称   
   			value:简单类型   
   			ref:应用
       		index:参数的索引
       		type:参数类型 
   		-->
           <constructor-arg name="dao" ref="userdao2"></constructor-arg>
       </bean>
   
   </beans>
   ```

6. 测试

   ```java
   
   /***
   * 程序需要就自己创建依赖对象,
   * 就产生了耦合
   */
   public static void main(String[] args) {
       //不使用反转
       //创建对象调用方法
       UserDao dao = new UserDaoImpl();
       dao.addUser();
       dao.deleteUser();
   }
   
   ----------------
   添加用户
   删除用户
   
   ```

   ```java
   <bean id="userdao" class="dao.impl.UserDaoImpl"></bean>
   <bean id="userdao2" class="dao.impl.UserDaoImpl2"></bean>
   
   //1. 加载配置文件(spring容器)
   		ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
   
   //2. 从容器中获取对象,根据<bean>标签的id的到创建的对象,后面写类类型规定类型
   UserDao bean = context.getBean("userdao", UserDao.class);
   UserDao bean2 = context.getBean("userdao2", UserDao.class);
   
   //3.对象调用方法
   bean.addUser();
   bean.deleteUser();
   bean2.addUser();
   bean2.deleteUser();
   ```

   ```java
   //构造器/setter方法
   <bean id="userservice" class="service.impl.UserServiceImpl">
   	<!-- 属性标签,借助属性的set访问器赋值:
   		name:属性的名称 
   		value:简单类型的值 
   		ref:应用 
   	-->
   	<property name="dao" ref="userdao"></property>
   </bean>
   
   -------------------------
   
   UserDao dao;
   
   //setter方法
   public void setDao(UserDao dao) {
       this.dao = dao;
   }
   
   --------------------------------------
   /**
   * service 需要调用dao,之前是直接写
   * 通过spring容器给对象的属性赋值
   */
   
    	// 1. 加载配置文件(spring容器)
       ApplicationContext context = new ClassPathXmlApplicationContext(
       "applicationContext.xml");
   
   	// 2. 从容器中获取对象,根据<bean>标签的id的到创建的对象,后面写实现类类型规定类型
   
   	UserServiceImpl bean = context.getBean("userservice", UserServiceImpl.class);
   
   	// 3.对象调用方法
   	bean.addUser();
   	bean.deleteUser();
   
   ```



   > 使用`<constructor-arg>`是通过调用带参构造实现的
   >
   > 如果要使用无参构造和访问器,一定要给出
   >
   >
   > 给出了带参构造之后,要手动给出默认无参构造,(...当时这里出现问题,好久才知道原因,感觉自己傻傻的...)
   >
   > 否则会报错:org.springframework.beans.BeanInstantiationException: Failed to instantiate [service.impl.UserServiceImpl]: No default constructor found;

   ```java
   <!-- 使用带参构造 -->
   <bean id="userService2" class="service.impl.UserServiceImpl">
       <!-- name:名称 //value;简单类型// ref:引用 //index: 参数索引 type:参数类型 -->
       <!-- 
      看到源码: 通过name value//ref对属性赋值 
      name="dao" ref="userdao"
     -->
       <!-- 
      不知道源码: 通过index value/ref进行赋值
      当构造函数有多个参数时，可以使用constructor-arg标签的index属性，index属性的值从0开始。    
      index="1" ref="userdao"
    -->
       <constructor-arg index="0" ref="userdao" ></constructor-arg>
       <!-- <constructor-arg name="dao" ref="userdao2"></constructor-arg> -->
   </bean>
   
   ---------------------
   UserDao dao;
   public UserServiceImpl(UserDao dao) {
       this.dao = dao;
   }
   
   public UserServiceImpl() {
   }
   --------------------------
   // 1. 加载配置文件(spring容器)
       ApplicationContext context = new ClassPathXmlApplicationContext(
       "applicationContext.xml");
   
   // 2. 从容器中获取对象,根据<bean>标签的id的到创建的对象,后面写实现类类型规定类型
   
   UserServiceImpl bean = context.getBean("userservice2", UserServiceImpl.class);
   
   // 3.对象调用方法
   bean.addUser();
   bean.deleteUser();
   ```


# 注解实现装配

> 导入spring-aop-4.1.6.RELEASE.jar

1.  @Component   `   <bean id="" class="">`基本注解，标识一个受Spring管理的Bean组件 
2. @Controller:表示层
3. @service:业务逻辑层
4. @repository:数据持久层,注解指定此类是一个容器类，是DA层类的实现。标识持久层Bean组件 
5. @Scope指定此spring bean的scope是单例 
6. @Resources: 实现依赖注入
7. @Autowired: 注解告诉spring，这个字段需要自动注入
8. @Qualifie(""):实现依赖注入

> 6.7二个标签联合使用

## 开发

1. 建立javaproject 导入(4+1)和spring-aop-4.1.6.RELEASE.jar

2. 编写dao接口和实现类

3. 编写service接口和实现类

4. 编写**applicationContext.xml**

   > 配置完`<context:component-scan>`标签后，spring就会去自动扫描`base-package`对应的路径或者该路径的子包下面的java文件，如果扫描到文件中带有@Service,@Component,@Repository,@Controller等这些注解的类，则把这些类注册为bean 

   > 在注解后加上例如@Component(value=”abc”)时，注册的这个类的bean的id就是adc.

   > `use-default-filters`属性的默认值为true,这就意味着会扫描指定包下标有@Service,@Component,@Repository,@Controller的注解的全部类，并注册成bean。 
   >
   > `<context:component-scan base-package="com.sparta.trans"/>` Use-default-filter此时为true

   > 如果你**只想扫描指定包**下面的Controller，那该怎么办？此时子标签`<context:incluce-filter>`就可以发挥作用了。

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans 
           http://www.springframework.org/schema/beans/spring-beans.xsd
           http://www.springframework.org/schema/context 
           http://www.springframework.org/schema/context/spring-context.xsd">
           
        <context:component-scan base-package="dao,service"></context:component-scan>  
      
   </beans>      
   ```

5. 给dao实现类添加注解

   ```java
   package dao.impl;
   
   import org.springframework.stereotype.Repository;
   
   import dao.UserDao;
   
   //设置实现类对象的id为userdao,可以通过配置文件使用
   @Repository("userdao")
   public class UserDaoImpl implements UserDao {
   
       @Override
       public void addUser() {
           System.out.println("添加用户");
       }
   
       @Override
       public void deleteUser() {
           System.out.println("删除用户");
       }
   
   }
   
   ```

6. 给Service实现类添加注解

   ```java
   package service.impl;
   
   import org.springframework.beans.factory.annotation.Autowired;
   import org.springframework.beans.factory.annotation.Qualifier;
   import org.springframework.stereotype.Service;
   
   import dao.UserDao;
   import service.UserService;
   
   //设置实现类对象的id为userdao2,可以通过配置文件使用
   @Service("userservice")
   public class UserServiceImpl implements UserService {
   
   	// 接口 + 构造器/setter方法
   	UserDao dao;
   
   	public UserServiceImpl() {
   	}
   
   	// 1.构造器
   	public UserServiceImpl(UserDao dao) {
   		this.dao = dao;
   	}
   
   	// 2.setter方法
   	@Autowired
   	@Qualifier("userdao")
   	//这样Spring会找到id为userdao的bean进行装配。
   	public void setDao(UserDao dao) {
   		this.dao = dao;
   	}
   
   	@Override
   	public void addUser() {
   		dao.addUser();
   	}
   
   	@Override
   	public void deleteUser() {
   		dao.deleteUser();
   	}
   
   }
   
   ```

## 遇到的问题

```java
org.springframework.beans.factory.BeanDefinitionStoreException: Unexpected exception parsing XML document from class path resource [com/ckinghan/spring7_auto/beans.xml]; nested exception is java.lang.NoClassDefFoundError: org/springframework/aop/TargetSource
```

> 解决办法:**导入spring-aop-4.1.6.RELEASE.jar**

# 代理

> 访问真实的对象之前和之后对请求进行预处理以及过滤等功能



## 静态代理

> 在编译期间,为每个真实类创建代理类在代理类中提供预处理和过滤功能
>
> 好处: 业务分离
>
> 缺点:必须给每个接口写代理类，代理方法代码不能复用

### 实现

1. 新建一个代理类实现dao的接口
2. 代理类进行业务处理与过滤功能
3. 测试
4. 多态创建代理类的对象
5. 对象调用dao的方法

```java
//对dao的实现类进行代理,在之前之后添加功能
package dao.proxy;

import dao.IUserDao;
import dao.impl.UserDaoImpl;

public class UserDaoProxy implements IUserDao {
    UserDaoImpl dao ;

    public UserDaoProxy(UserDaoImpl dao) {
        this.dao=dao;
    }

    @Override
    public void addUser() {
        // TODO Auto-generated method stub
        System.out.println("开启事务..");
        dao.addUser();
        System.out.println("关闭事务");

    }

    @Override
    public void deleteUser() {
        // TODO Auto-generated method stub
        System.out.println("开启事务..");
        dao.deleteUser();
        System.out.println("关闭事务");

    }

}

```

> 测试

```java
package Test;

import dao.IUserDao;
import dao.impl.UserDaoImpl;
import dao.proxy.UserDaoProxy;

public class Test {
    public static void main(String[] args) {

        IUserDao dao = new UserDaoProxy(new UserDaoImpl());
        dao.addUser();
        dao.deleteUser();

    }
}
---------------------------------
开启事务..
创建user1对象
关闭事务
开启事务..
销毁user1对象
关闭事务

```



## 动态代理

> **在运行**期间,为每个真实对象创建代理对象的方式

jdk:动态代理:接口 + 实现类

cglib:动态代理:委托类

1. 创建委托类对象
2. 创建代理类对象
3. 设置父类(cglib)
4. 添加增强功能
5. 生产代理对象(cglib)
6. 代理调用方法



### jdk实现动态代理

> 不用编写一个代理类实现dao接口
>
> Proxy.newProxyInstance(loader, interfaces, InvocationHandler h)

```java
package Test;

import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;

import dao.IUserDao;
import dao.impl.UserDaoImpl;

public class ProxyTest {

    public static void main(String[] args) {
        //1.创建委托类对象
        IUserDao dao = new UserDaoImpl();
        //2.生成代理对象
        IUserDao proxy = (IUserDao) Proxy.newProxyInstance(
            ProxyTest.class.getClassLoader(), 
            dao.getClass().getInterfaces(),  //委托类对象实现的接口
            new InvocationHandler() {
                //proxy:代理类对象
                //method:委托类对象方法描述对象
                //args:方法的参数
                @Override
                public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                    System.out.println("开启事务");
                    //手动调用拦截的方法
                    Object obj = method.invoke(dao, args);
                    System.out.println("提交事务");
                    return obj;
                }
            });

        //3.调用代理对象方法
        proxy.addUser();
        proxy.deleteUser();


    }

}

```



### cglib动态代理

> 不用编写一个代理类实现dao接口
>
> 需要导入cglib,asm的.jar,但是spring core.jar中包含了这些jar

```java
package Test;

import java.lang.reflect.Method;

import org.springframework.cglib.proxy.Enhancer;
import org.springframework.cglib.proxy.MethodInterceptor;
import org.springframework.cglib.proxy.MethodProxy;

import dao.impl.UserDaoImpl;

public class CglibTest {

    public static void main(String[] args) {
        //1.创建委托类对象
        UserDaoImpl userdao = new UserDaoImpl();
        //2.创建代理类对象
        Enhancer enhancer = new Enhancer();
        //设置父类
        enhancer.setSuperclass(UserDaoImpl.class);
        //添加增强功能
        enhancer.setCallback(new MethodInterceptor() {

            @Override
            public Object intercept(Object proxy, Method method, Object[] args, MethodProxy proxyMethod) throws Throwable {
                System.out.println("开启事务");
                Object obj = method.invoke(userdao, args);
                System.out.println("提交事务");
                return obj;
            }

        });
        UserDaoImpl proxy = (UserDaoImpl) enhancer.create();
        //3.调用
        proxy.addUser();
        proxy.deleteUser();
    }

}

```



# spring aop面向切面编程

> 采用横向抽取方式,在运行阶段,动态向目标对象织入增强代码的一种思想,底层采用原理:动态代理


1. 原理

	> jdk 和cglib动态代理

2. 应用场景

	> 事务管理,性能检测,日志管理,缓存...

3. aop框架

	1. spring aop

	2. aspectj
	3. jboss



## aop 专业术语

1. target:目标对象(需要添加增强代码的对象)
2. advince:通知,增强代码类的对象
3. jointpoint:连接点目标对象的方法
4. pointCut;切入点,添加了增强代码的方法
5. weaver:织入(动态过程),增强代码添加到目标对象方法的过程
6. aspect:切面,增强代码和目标对象的方法之间形成的面



# aop的开发



1. 新建立java project---- 导入jar(9个)
   1. 4+1
   2. spring -aop.jar
   3. spring-aspects:aspectj的规范
   4. aopoliance.jar:aop联盟,设置通知的规范
   5. aspectjweaver-1.8.5.jar:织入的实现

2. 编写dao接口和实现类,添加注解

3. 编写Service接口和实现类,添加注解

4. 抽取出增强代码类aspect----MyAspect.java,写入增强代码

5. 修改applicationContext.xml

   1. `<cop:config>`:声明代理的方式,默认jdk代理

   2. 配置切入点`<aop:pointcut>`

      > 使用id标识切入点

      > 使用expression表达式找到需要切入的方法
      >
      > 格式:exection(访问修饰符 包名.类型.方法名(参数列表)),*号代表通配符
      >
      > exection(* srevice.impl.UserServiceImpl.*()):UserServiceImpl类下的无参方法
      >
      > exection(* srevice..*(..)): service包下的所有类和所有方法
      >
      >

   3. 配置切面`<aop:aspecct>`

      > ref:增强代码的对象(目标类的实现类的对象)

      1. 前置增强:方法调用之前的增强代码`<aop:before  />`

         > `<pointcut-ref>`:声明那个切入点(切入点的id)
         >
         > `<method>`:增强的方法

      2. 后置增强:方法调用之后执行的增强代码`<aop:after />`

         > `<pointcut-ref>`:声明那个切入点(切入点的id)

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xmlns:context="http://www.springframework.org/schema/context"
          xmlns:aop="http://www.springframework.org/schema/aop"
          xsi:schemaLocation="http://www.springframework.org/schema/beans 
                              http://www.springframework.org/schema/beans/spring-beans.xsd
                              http://www.springframework.org/schema/context 
                              http://www.springframework.org/schema/context/spring-context.xsd
                              http://www.springframework.org/schema/aop 
                              http://www.springframework.org/schema/aop/spring-aop.xsd">
   
       <!--1.创建目标类对象  -->
       <bean id="userservice" class="service.impl.UserServiceImpl"></bean>   
       <!--2.创建增强代码类对象  -->   
       <bean id="myaspect" class="aspect.MyAspect"></bean>
       <!--3.aop织入增强代码   proxy-target-class:true代表强制使用cglib动态代理 --> 
       <aop:config>
           <!-- 切入点: 找方法     id:切入点标识      expression:表达式  -->
           <aop:pointcut expression="execution(* service..*(..))" id="myPointCut"/>
           <!-- 配置切面:  增强代码+切入点   ref：增强代码对象 -->
           <aop:aspect ref="myaspect">
               <!-- 前置增强  -->
               <aop:before method="before" pointcut-ref="myPointCut"/>
               <!-- 后置增强 -->
               <aop:after-returning method="after" pointcut-ref="myPointCut"/>
           </aop:aspect>
       </aop:config>
   </beans>      
   ```

6. 测试

   ```java
   package test;
   
   import org.springframework.context.ApplicationContext;
   import org.springframework.context.support.ClassPathXmlApplicationContext;
   
   import service.IUserSerivice;
   
   public class Test {
       public static void main(String[] args) {
           // 1.加载容器
           ApplicationContext context = new ClassPathXmlApplicationContext(
               "applicationContext.xml");
   
           // 2.获取目标对象
           IUserSerivice service = context.getBean("userService",
                                                   IUserSerivice.class);
   
           // 3.调用方法
           service.addUser();
           service.deleteUser();
       }
   }
   
   ```

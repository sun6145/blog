---
title: javaeday11mybatis框架
tags:
  - null
  - null
date: 2018-9-7 23:38:23
categories:
top:
---

# 什么是框架

	将繁琐的,重复性工作封装起来,使开发者能够将更多精力放在业务分析的理解上的这套程序.

1. 框架的优点
   1. 简化开发,提升开发效率
   2. 屏蔽细节,安全性高
   


# Mybatis框架

1. 传统jdbc的问题
   1. 连接的四个参数有硬编码问题,(配置文件)
   2. sql的定制不够灵活
   3. 频繁打开和关闭连接

> 是一个持久层框架,支持定制化sql,存储过程和高级映射
>
> mybatis通过xml或者注释实现定制
>
> mybatis是一个orm框架
>
> 	orm:对象关系映射
>		
> 	(java对象和关系型数据库表记录之间的映射问题)

> 关注点
>
> 1. sql
> 2. 输入的参数
> 3. 输出结果

<!-- more -->

# 入门案例

1. jar
   1. mybatis:核心jar
   2. 数据库的connection-jar
2. 底层封装jdbc
   1. 注册驱动
   2. 获取链接(全局配置文件:连接池配置 事务管理器)
   3. 发送sql(映射文件:定制化sql(输入参数和输出结构的映射))
   4. 接收处理(java 代码读取配置文件调用api测试)
   5. 释放资源

## 步骤:

1. 导入jar包

2. src--pojo

3. 新建立一个source File--config

4. config下建立jdbc.properties

5. config下创建mybatis.xml文件

   5.1 加载配置文件

   ```xml
   <properties resource="jdbc.properties"></properties>
   ```



   5.2 起别名

   - 设置单一别名
   - 批量设置别名

   ```xml
   <typeAliases>
       <!-- 设置单一别名 -->
       <typeAlias type="pojo.User" alias="user" /></typeAliases>
   ```

   ```xml
   <typeAliases>
   		<!-- 批量起别名 默认的名称为类名 -->
   		<package name="pojo" />
   	</typeAliases>
   ```

   5.2.3 配置环境



   ​	1. 数据源

   ​	2. 事务管理器

   ```xml
   <environments default="development">
       <!-- 具体环境:id:当前环境的唯一标识 -->
       <environment id="development">
           <!-- 事务管理器 type:事务管理器的类型 -->
          <transactionManager type="JDBC" />
                               
   		<!-- 数据源 type:pooled 连接池管理 -->
           <dataSource type="POOLED">
               <property name="driver" value="${driver}" />
               <property name="url" value="${url}" />
               <property name="username" value="${username}" />
               <property name="password" value="${password}" />
           </dataSource>
           
       </environment>
   </environments>
   ```

   5.3.4 配置映射器

   ​	1. 单个映射

   ```xml
   <mappers>
       <mapper resource="UserInfoMapper.xml" />
   </mappers>
   ```



    2. 批量加载映射文件
    
       >  mapper接口和mapper映射文件必须同包同名  -->

   ```xml
   <mappers>
       <!-- 批量加载映射文件:  mapper接口和mapper映射文件必须同包同名  -->
       <package name="mapper"/>
   </mappers>
   ```


> 具体的mybatis.xml

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <!-- 约束dtd/schema -->
   <!DOCTYPE configuration
     PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
     "http://mybatis.org/dtd/mybatis-3-config.dtd">
   
   
   <!-- 环境 -->
   
   <configuration>
   
       <!-- 配置文件 -->
       <properties resource="jdbc.properties"></properties>
       <typeAliases>
           <!-- 设置单一别名 -->
           <!-- <typeAlias type="pojo.User" alias="user" /> -->
           <!-- 批量起别名 默认的名称为类名 -->
           <package name="pojo" />
       </typeAliases>
       <environments default="development">
           <!-- 具体环境:id:当前环境的唯一标识 -->
           <environment id="development">
               <!-- 事务管理器 type:事务管理器的类型 -->
               <transactionManager type="JDBC" />
               <!-- 数据源 type:pooled 连接池管理 -->
               <dataSource type="POOLED">
                   <property name="driver" value="${driver}" />
                   <property name="url" value="${url}" />
                   <property name="username" value="${username}" />
                   <property name="password" value="${password}" />
               </dataSource>
           </environment>
       </environments>
       <!-- 映射器 -->
       <mappers>
           <mapper resource="UserInfoMapper.xml" />
       </mappers>
   </configuration>
   ```



6. config--建立UserInfoMapper.xml

   >  映射器的名字与mybatis.xml中单个定义时候相同

   	1. `#{name}`:表示一个**占位符**
		
   		为string,基本类型的时候,名字随便写
		
   		如果传入的参数是pojo类型,那么#{}中的变量名称必须是pojo中的属性.属性.属性…

    2. `${value}`:表示**拼接符:字符串原样拼接** 

       	拼接符有可能造成sql注入

> 具体的UserInfoMapper.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<!-- namespace:命名空间(隔离sql) -->
<mapper namespace="DeptMapper">

    <!-- 根据id查询dept的表中的数据 -->
    <select id="selectDeptinfo" resultType="pojo.User">
        select * from dept where
        deptno =#{deptno}
    </select>
    <!-- 查询全部的数据,返回的是list,用User接收-->
    <select id="selectAll" resultType="pojo.User">
        select * from dept
    </select>

    <!-- 模糊查询 -->
    <select id="selectlikeName" resultType="pojo.User">
        select * from dept where dname like '%${value}%'
    </select>


    <!-- 添加 -->
    <insert id="insertInfo">
        <!-- value与实体类中的一致 -->
        insert into dept(deptno,dname,loc) values(#{deptno},#{dname},#{loc})
    </insert>
</mapper>
```



# 增删改查案例

> 准备阶段和入门案例一样,对UserInfoMapper.xml进行操作
>
> 1. 增`<insert id=" ">SQL	</insert>`
> 2. 删`<delete id="">SQL	</delete>`
> 3. 改`<update id="">SQL	</update>`
> 4. 查`<select id=" ">SQL	</select>`

> 具体代码:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<!-- namespace:命名空间(隔离sql) -->
<mapper namespace="DeptMapper">
    <!--查询 -->
    <select id="selectDeptinfo" resultType="pojo.User">
        select * from dept where
        deptno =#{deptno}
    </select>

    <select id="selectAll" resultType="pojo.User">
        select * from dept
    </select>

    <!-- 模糊查询 -->
    <select id="selectlikeName" resultType="pojo.User">
        select * from dept where
        dname like '%${value}%'
    </select>


    <!-- 添加 -->
    <insert id="insertDept">
        <!-- value与实体类中的一致 -->
        insert into dept(deptno,dname,loc) values(#{deptno},#{dname},#{loc})
    </insert>

    <!--修改 -->
    <update id="updateDept">
        update dept set dname=#{dname},loc=#{loc} where
        deptno =#{deptno}
    </update>

    <!-- 删除 -->
    <delete id="deleteDept">
        delete from dept where deptno = #{deptno}
    </delete>
</mapper>
```

> 测试

```java
public static void main(String[] args)  {
    try {
        // 1.加载配置文件
        Reader rd = Resources.getResourceAsReader("mybatis.xml");
        SqlSessionFactory ssf = new SqlSessionFactoryBuilder().build(rd);
        // 2.SqlSession的方法来处理增删改查
        //	SqlSession session = ssf.openSession();
        //开启事务自动提交
        SqlSession session = ssf.openSession(true);
        //使用映射文件的命名空间和操作的id
        // User info = session.selectOne("DeptMapper.selectDeptinfo", 10);
        // List<User> info = session.selectList("DeptMapper.selectAll");

        // List<User> info =session.selectList("DeptMapper.selectlikeName","s");
        User user = new User(1,"zs","654321");
        int info =session.insert("DeptMapper.insertDept",user);
     
        //释放资源
        session.close();
    } catch (IOException e) {
        // TODO Auto-generated catch block
        e.printStackTrace();
    }
}
```



# mapper代理实现dao开发

> Mapper接口开发方法只需要编写Mapper接口（实际上相当于DAO接口）然后由Mybatis根据接口定义创建接口的动态代理对象。换句话说也就是不用自己编写DAO接口的实现类了。

要求:

**1.    mapper接口和mapper映射文件必须同包同名**

**2． mapper映射的namespace和接口全限定名称保持一致**

**3． mapper标签的id值和接口的方法名称一致**

**4． 标签的输入参数和接口方法的输入参数类型一致**

**5． 标签的结果类型和接口方法的返回值类型一致**

![](https://ws1.sinaimg.cn/mw690/005Ik064gy1fv1fh2g06uj30eh0dcdgg.jpg)

实现dao之前,编写了mybatis.xml

 	1. 加载了配置文件
 	2. 对pojo下的类批量起别名
 	3. 配置了环境
 	4. 批量加载mapper下的映射文件

> UserInfoMapper.java

```java
package mapper;

import pojo.User;

public interface UserInfoMapper {
    User selectById(int uid);
}

```

> UserInfoMapper.xml

```java
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
    "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

    <!-- namespace写接口的全限定名称 -->
    <mapper namespace="mapper.UserInfoMapper">

	<!-- id和接口的方法名称一致 -->

    <!-- 标签的输入参数和接口方法的输入参数类型一致 -->
    <!--  标签的结果类型和接口方法的返回值类型一致 -->
    <select id="selectById" parameterType="_int" resultType="user">
		select * from dept where deptno=#{deptno}
	</select>	
</mapper>
```

> 测试

```java
public static void main(String[] args) throws IOException {
    // 1.加载配置文件mybatis.xml
    Reader rd = Resources.getResourceAsReader("mybatis.xml");
    // 使用工厂模式创建SqlSession
    SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(rd);
    //自动提交为true
    SqlSession Session = factory.openSession(true);
    
    //获取Mapper接口的实现类对象 mapper
    UserInfoMapper mapper = Session.getMapper(UserInfoMapper.class);
    
    User info = mapper.selectById(10);

    System.out.println(info);
}
```



## 使用注解实现

> mapper代理要写mapper的接口和映射文件
>
> 可以写在一个接口中,在接口中使用注解减少工作量

> UserInfoMapper2.java文件

```java
public interface UserInfoMapper2 {
	@Select("select * from dept where deptno = #{deptno}")
	User selectById(int uid);
}
```



# 高级映射

> resultType:
>
> 	结果集字段名称和属性名一致则映射成功;
>	
> 	结果集字段和属性名部分一致则部分映射成功;
>	
> 	   结果集字段和属性名都不一致不会创建对象。

## 一对多

```java
<!-- type:对象类型  orm -->
    <resultMap type="dept" id="deptMap">
    	<!--  主键字段映射  -->
    <id column="deptno" property="deptno"/>
    	<!-- 非主键字段映射  -->
    <result column="dname" property="dname"/>
    	<result column="loc" property="loc"/>
    	<!-- 封装结果到集合    property:属性    javaType:每个对象类型 -->
            <collection property="emps" ofType="emp" column="deptno">
    		<id column="empno" property="empno"/>
    		<result column="ename" property="ename"/>
    		<result column="job" property="job"/>
    		<result column="hiredate" property="hiredate"/>
    		<result column="mgr" property="mgr"/>
    		<result column="sal" property="sal"/>
    		<result column="comm" property="comm"/>
    		<result column="deptno" property="deptno"/>
    	</collection>
    </resultMap>
```

## 一对一

```java
<resultMap type="emp" id="empMap">
    	<id column="empno" property="empno"/>
   		<result column="ename" property="ename"/>
   		<result column="job" property="job"/>
   		<result column="hiredate" property="hiredate"/>
   		<result column="mgr" property="mgr"/>
   		<result column="sal" property="sal"/>
   		<result column="comm" property="comm"/>
   		<result column="deptno" property="deptno"/>
   		<!-- 封装结果到对象中 -->
    <association property="dept" column="deptno" javaType="dept">
   			<id column="deptno" property="deptno"/>
	    	<result column="dname" property="dname"/>
	    	<result column="loc" property="loc"/>
   		</association>
    </resultMap>
	<select id="findEmpAndDept" resultMap="empMap">
	  select * from emp left join dept on emp.deptno = dept.deptno
	</select>
```


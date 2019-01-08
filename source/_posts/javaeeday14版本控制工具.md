---
title: javaeeday14版本控制工具
tags:
  - null
  - null
date: 2018-09-12 09:29:17
categories:
top:
---


SVN 和 Maven
<!-- more -->


# 版本控制工具

> 集中式的版本控制工具
>
> 需要安装客户端和服务器端

## 集中式

1. 开始开发,从服务器端**检出**(checkout),获取版本号为0;
2. 更新项目,添加内容**,提交(commit)**后,服务器端版本号增加
3. 下一个人员操作,**先更新后提交**





> 集中式: 所有代码的托管都在服务器端
>
> 版本号从0开始
>
> 操作的时候,先更新后提交



> 应用: svn应用于局域网
>
> 集中式,需要备份服务器
>
> 更新,速度慢



## 分布式

> 每一台机器都当做是服务器



# SVN的使用

1. 安装
   - 服务器端工具
   - a安装客户端工具

## eclipse使用svn

1. 在eclipse安装目录创建svn文件夹

2. 将features和plugins目录复制到svn

3. 在dropiins目录下创建svn.link病编辑

   > path=eclipse创建的svn的目录

4. 删除configration目录下的org.eclipse.update文件夹

5. 项目--右键,选择`Team`---`share project`

# Maven

> 是项目构建,依赖管理,项目信息管理的工具
>
> 不涉编码 编码方式



1. eclipse:

   > 手工操作较多 ide的配置有差异

2. ant:项目构建工具

3. maven:项目管理和依赖管理工具

   1. 基于pom(项目对象模型),xml配置

      a) 项目依赖

      b) 构建信息

   2. 对项目的目录有一定约束(优先于配置文件)
   3. 依赖管理:提供中央仓库





## 使用

> 需要jdk1.7以上
>
> http://mvnrepository.com/ 定位[jar](http://mvnrepository.com/)

1. [下载安装](http://maven.apache.org/download.cgi)

2. 使用

   1. bin:可执行文件
   2. boot:启动依赖的jar
   3. conf: 配置Maven的信息
      1. 配置本利仓库的路径
      2. 中央仓库路径(私服)
      3. jdk的版本
   4. lib: 依赖的jar

3. 配置环境变量

   1. M2_HOME=maven安装路径
   2. PATH=%M2_HOME%\bin

4. 测试:mvn -v

   ```xml
   C:\Users\sun>mvn -v
   Apache Maven 3.5.2 (138edd61fd100ec658bfa2d307c43b76940a5d7d; 2017-10-18T15:58:13+08:00)
   Maven home: D:\develop\maven\apache-maven-3.5.2\bin\..
   Java version: 1.8.0_144, vendor: Oracle Corporation
   Java home: D:\develop\Java\jdk1.8.0\jre
   Default locale: zh_CN, platform encoding: GBK
   OS name: "windows 10", version: "10.0", arch: "amd64", family: "windows"
   ```

   ## eclipse使用

   ### 骨架方式



# Maven开发mybatis

1. 在main /test 目录下添加resource file 文件名 resource

2. 新建实体类

3. 编写mybatis.xml文件,在src/main/目录下编写

   > **需要编写在起别名的时候用全限定名称**
   >
   > ```xml
   > <typeAliases>
   >     <package name="com.lee.mybatisdemo.pojo" />
   > </typeAliases>
   > 
   > <mappers>
   >     <!-- <mapper resource=" deptMapper.xml" /> -->
   >     <!--批量加载配置文件 -->
   >     <package name="mappecom.lee.mybatisdemo.mapper" />
   > </mappers>
   > ```
   >
   >

4. 编写main/java/com.lee.mybatisdemo.mapper/UserMapper.java通过注解select等等

5. 也使用配置文件,同包同名UserMapper.xml

6. 配置文件应该放在main/resource包下

7. 新建main/resource/com.lee.mybatisdemo.mapperuserMapper.xml

8. 编写xmL的内容

9. 导入数据库连接/mybatis/log4j/junit的包

10. run as  选择第一个build 填写 compile

11. **出现问题,运行环境要是jdk,修改**

12. 选择run as 测试,src/test/java/Test/Test.java新建立一个test方法

13. 通过Maven进行测试



# Maven 开发web项目

1. 新建立一个maven项目

2. 勾选create a simple project 

3. 填写group id,artiface id ,version packaging打包为war

4. 建立项目后会包错,没有web.xml和web-INF路径

5. 修改项目的特性

6. 项目右键 --properties-project faets  ,修改dynamic web module为3.1

7. 右键-javaee tools 工具生成

8. 新建jsp页面src/main/webapp

9. 新建立一个index.jsp

   > **报错:The superclass "javax.servlet.http.HttpServlet" was not found on the Java Build Path**
   >
   > 当前项目没有HttpServlet这个类 ,在servelet-api中
   >
   > **解决:在pom导入servlet-api**

10. 在sec/main/java下编写servlet类

11. 测试

    1. 建项目打包为一个war包
    2. 发布到tomcat上
    3. 启动tomcat,进行访问

12. 右键项目,runas  --maven builde  输入:`package` 打包

13. 可以在target目录下得到`servletdemo-0.0.1-SNAPSHOT.war`

14. 名字太长,我们可以在pom.xml打包起名字

    ```xml
    <build>
        <finalName>test</finalName>
    </build>
    ```

15. 清理 `clean`  ,重新打包`package`;可以联合到一起`clean package`

16. 将生成的test.war得到放到tomcat,tomcat会默认对war进行解包

    1. 将生成的jar包放到tomcat的webapps下
    2. 运行tomcat
    3. webapps会得到test文件夹
    4. 打开浏览器: http://127.0.0.1:8080/test/

17. 这样打包放上去比较麻烦,有问题需要修改后打包,放到tomcat

18. 清理项目,配置直接运行项目

19. 在`<build>`标签内添加插件标签`<plugin>`

    ```xml
    <build>
        <finalName>test</finalName>
        <plugins>
            <plugin>
                <!-- 配置插件 -->
                <groupId>org.apache.tomcat.maven</groupId>
                <artifactId>tomcat7-maven-plugin</artifactId>
                <!-- 对当前项目的tomcat进行配置 -->
                <configuration>
                    <port>8080</port>
                    <!-- 当前项目用"localhost:8080/servlet"就可以访问 -->
                    <path>/servlet</path>
                </configuration>
            </plugin>
        </plugins>
    
    </build>
    
    ```

20. 运行tomcat的插件:

21. 项目右键,运行run as maven build: `tomcat7:run`


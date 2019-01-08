---
title: java基础day18
tags:
  - 锁
  - 类的生命周期
  - 类加载器
  - 反射
date: 2018-08-21 09:02:59
categories:
top:
---
锁
类加载
反射
基本注解
<!-- more -->

# ReentrantLock锁

```java
try{
    加锁lock;
}finally{
    释放锁unlock
}
```

释放锁应该写在finally{}中,除非虚拟机退出,否则一定会释放,如果不写在finally中,当方法return后,锁便不能释放.

## 构造方法:

```java
// 创建锁对象
ReentrantLock lock = new ReentrantLock();

// 创建公平锁对象
// 会将锁给等待时间长的对象
ReentrantLock lock = new ReentrantLock(true);
```



卖票:

```java
class Ticket implements Runnable{
    private int ticketCount = 20;
    //创建锁
    ReentrantLock lock = new ReentrantLock();
    try{
        //上锁
        lock.lock();
        this.ticketCount-=1;
        System.out.println(Thread.currentThread().getName()+"卖了1张,剩余:"+this.ticketCount);
    }catch(Exception e){
        e.printStackTrace();
    }fianlly{
        //释放锁
        lock.unlock();
    }
}

public class TestSubThread2{
    public static void main(String[] args){
        Ticket ticket = new Ticket();
        Thread win1 = new Thread(ticket,"win1");
        Thread win2 = new Thread(ticket,"win2");
        win1.start();
        win2.start();
    }
}

```





## 释放锁

### sleep和wait()

sleep(毫秒值):放弃cpu的执行权,但是不释放锁

wait(可选毫秒值):放弃cpu和锁

notify():唤醒等待池中的一个线程

notifyAll():唤醒等待池中的所有线程

```java

class NumberDemo implements Runnable {
    int i = 10;//

    synchronized public void run() {
        for (; i >= 0; i--) {
            if (i == 5 && Thread.currentThread().getName().equals("t1")) {
                try {
                    // Thread.sleep(1000);
                    wait();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
            if (i == 0) {
                notify();
                System.out.println("醒了");
            }
            System.out.println(Thread.currentThread().getName() + ":" + i);
        }
    }
}

public class TestSubThread5 {
    public static void main(String[] args) {
        NumberDemo demo = new NumberDemo();
        Thread t1 = new Thread(demo, "t1");
        Thread t2 = new Thread(demo, "t2");
        t1.start();
        t2.start();
    }

}

//---wait----------------------
t1:10
t1:9
t1:8
t1:7
t1:6
t2:5
t2:4
t2:3
t2:2
t2:1
醒了
t2:0
t1:-1

//----sleep-----------------------
    t1:10
t1:9
t1:8
t1:7
t1:6
(等1s)
t1:5
t1:4
t1:3
t1:2
t1:1
醒了
t1:0

```



## 死锁问题

```java
class Zhangsan{
    public void say() {
        System.out.println("你给我书，我就给你画");
    }
    public void get() {
        System.out.println("张三获得了书");
    }
}
class Lisi{
    public void say() {
        System.out.println("你给我画，我就给你书");
    }
    public void get() {
        System.out.println("李四获得了画");
    }
}
class ThreadDemo implements Runnable{
    private static Zhangsan zhangsan = new Zhangsan();
    private static  Lisi lisi = new Lisi();
    public boolean tag = false;
    public void run() {
        if(tag) {
            synchronized (zhangsan) {
                zhangsan.say();
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                synchronized (lisi) {
                    zhangsan.get();
                }

            }
        }else {
            synchronized (lisi) {
                lisi.say();
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                synchronized (zhangsan) {
                    lisi.get();
                }
            }
        }
    }
}
public class TestSubThread4 {

    public static void main(String[] args) {
        ThreadDemo demo1 = new ThreadDemo();
        demo1.tag = true;
        ThreadDemo demo2 = new ThreadDemo();
        demo2.tag = false;
        Thread t1 = new Thread(demo1);
        Thread t2 = new Thread(demo2);
        t1.start();
        t2.start();

    }

}
```



## 生产消费

```java
class Qingfeng{
    private int count ;
    boolean tag = false;
    //产包子
    synchronized public void put(int count) {
        if(tag) {//if(true)
            try {
                wait();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
        //false
        this.count = count;
        System.out.println("生产了：" + this.count);
        tag = true;//有
        notify();//唤醒销售人员
    }
    //卖包子
    synchronized public void get() {
        if(tag == false) {
            try {
                wait();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
        //true
        System.out.println("卖了 ：" + this.count);
        tag = false;//卖，没了
        notify();//唤醒厨师
    }
}
```

```java
//生产
class Producer implements Runnable{
    Qingfeng qingfeng;
    Producer(Qingfeng qingfeng){
        this.qingfeng = qingfeng;
    }
    public void run() {
        for(int i = 1; i <= 5; i++) {
            qingfeng.put(i);
        }
    }
}
```

```java
//销售
class Cosumer implements Runnable{
    Qingfeng qingfeng;
    Cosumer(Qingfeng qingfeng){
        this.qingfeng = qingfeng;
    }
    public void run() {
        for(int i = 1; i <= 5; i++) {
            qingfeng.get();
        }
    }
}
```



# java应用程序

一个java程序只有一个main方法,下面中的TestA1和TestA2有二个main方法,实际上互相不干扰.结果都是6.

```java
package day18.loader;

public class A {
    public static int a = 5;
}

```

```java
package day18.loader;

public class TestA1 {

	public static void main(String[] args) {
		A.a ++;
		System.out.println(A.a);//6
	}
}
```

```java
package day18.loader;

public class TestA2 {

	public static void main(String[] args) {
		A.a ++;
		System.out.println(A.a);//6
	}
}
```



## 类的生命周期

1. 加载: 把字节码文件.class 加载到方法区的内存中,创建一个对应的对象,堆中(Class),通过Class对象可以获得字节码中的所有信息
2. 连接
   1. 验证:对字节码文件.class进行验证,保证虚拟机的安全
   2. 准备: 为静态变量分配空间,进行默认初始化
   3. 解析:把符号引用替换为直接引用(如把方法用指针替换)
3. 初始化:对静态变量声明处或静态块初始化
4. 使用:
5. 卸载

## 类的初始化

当一个类被主动使用时：

>当创建某个类的**新实例时**

>当调用某个类的静态成员；

>当初始化某个子类时，该子类的所有父类都会被初始化。

>当使用反射方法强制创建某个类或接口的对象时

>当虚拟机java命令运行启动类

注意：static final类型的不能导致初始化的情况

​	静态常量在编译期就**能确定值**的情况**不会引起初始化**

> 下面程序中,
>
> ​	访问n的时候,会引起类的初始化
>
> ​	访问num,不会引起类的初始化,在编译期期间就能确定了值是33.
>
> ​	访问sn2,也不会引起类的初始化,值为88.
>
> ​	访问sn的时候,虽然是final static修饰,但是值不能确定,会引起类的初始化

```java
package day18.loader;

class Demo{
    static int n = 55;
    final static int num=33;
    final static int sn = 33 + n;
    final static int sn2 =33+55;
    static {
        System.out.println("static 初始化");
    }
}
class SubDemo extends Demo{

}
public class TestDemo1 {
    /*static int sn = 45;
	static{
		sn = 55;
	}
	static {
		System.out.println("static !");
	}*/
    public static void main(String[] args) throws ClassNotFoundException {
        //		Demo demo = new Demo();
        System.out.println(Demo.sn);
        //		SubDemo demo = new SubDemo();
        //		Class.forName("day18.loader.Demo");

    }

}

```



## 类加载器

1. 根类加载器:加载`\jre\lib`下的核心类库

   Bootstrap ClassLoader

2. 扩展类加载器:加载路径`\jre\lib\ext`的文件

   Extension ClassLoader

3. 系统(应用)类加载器: 加载classPath路径下的文件(默认当前路径)

   App ClassLoader

   System ClassLoader

4. 自定义类加载器

   1. 加载自定义位置的文件

   ```flow
   st=>start: 用户自定义类加载器
   op=>operation: 系统类加载器
   op2=>operation: 扩展类加载器
   op3=>operation: 根类加载器
   op4=>input: aaa
   st->op->op2->op3
   
   ```

   ## 加载器的顺序

   系统类加载器->扩展类加载器->根类加载器->

   **如果根类加载器不能加载,看扩展类加载器,不能给系统类加载器,给用户自定义类加载器**

   

   **双亲委派模型**

   工作过程为：如果一个类加载器收到了类加载的请求，它首先不会自己去尝试加载这个类，而是把这个请求委派给父类加载器去完成，每一个层次的加载器都是如此，因此所有的类加载请求都会传给顶层的启动类加载器，只有当父加载器反馈自己无法完成该加载请求（该加载器的搜索范围中没有找到对应的类）时，子加载器才会尝试自己去加载。

   > 好处是Java类随着它的类加载器一起具备了一种带有优先级的层次关系。 
   >
   > 例如java.lang.Object类，无论哪个类加载器去加载该类，最终都是由启动类加载器进行加载，因此Object类在程序的各种类加载器环境中都是同一个类。 

```java
public class TestDemo2 {

    public static void main(String[] args) {
        //系统类（应用类）加载器
        ClassLoader loader = TestDemo2.class.getClassLoader();
        System.out.println(loader);
        //父扩展
        System.out.println(loader.getParent());
        //父根
        System.out.println(loader.getParent().getParent());

    }

}
//结果---------
sun.misc.Launcher$AppClassLoader@73d16e93
sun.misc.Launcher$ExtClassLoader@15db9742
null(根类加载器是由C语言写的)
```

## 加载类

```java
public static void main(String[] args) throws ClassNotFoundException {
    //加载类  完全限定命名
    ClassLoader.getSystemClassLoader().loadClass("day18.Demo1");
    //加载类 ，并初始化,Class.forNmae(加载类,初始化 true,系统类加载器 )
    Class.forName("day18.Demo1",true,ClassLoader.getSystemClassLoader());
    //简写
    Class.forName("day18.Demo1");
}
```



## 自定义类加载器

> 由于是双清委派,在项目外建立一个类C.java,编译得到C.Class

```java
//自定义类加载器
class MyLoader extends ClassLoader{
    String path;
    MyLoader(String path){
        this.path = path;
    }
    // 包名.类名
    @Override
    protected Class<?> findClass(String name) throws ClassNotFoundException {
        Class<?>  c = null;
        //     "d:/data/" + "C" + ".class" ->"d:/data/C.class"
        //带包情况下,用.替换
        //		path = path + name.replace(".", "/").concat(".class");
        path = path + name.concat(".class");

        FileInputStream fin = null;
        try {
            fin = new FileInputStream(path);
            byte [] b = new byte[fin.available()];
            int len = fin.read(b);
            c = this.defineClass(name, b, 0, len);			
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            if(fin != null) {
                try {
                    fin.close();
                } catch (IOException e) {
                    // TODO Auto-generated catch block
                    e.printStackTrace();
                }
            }
        }
        return c;
    }

}

```

```java
public class TestDemo4 {

    public static void main(String[] args) throws ClassNotFoundException {
        // 自定义类加载器 加载类 C .class
        MyLoader my = new MyLoader("d:/data/");
        Class c = Class.forName("C",true,my);
        System.out.println(c.getClassLoader());
        c = null;
    }

}
```



## 安全管理器

SecurityManager

> 在Java应用中，安全管理器是由System类中的方法setSecurityManager设置的。要获得当前的安全管理器，可以使用方法getSecurityManager。 

## 自定义安全管理器

```java
//自定义的安全管理器
class MySecurityManager extends SecurityManager{
	@Override
	public void checkRead(String file) {
		if(file.endsWith(".txt")) {
			throw new SecurityException("不能读取 txt文件");
		}
	}
	
}
public class TestDemo5 {

	public static void main(String[] args) throws IOException {
//		System.setSecurityManager(new MySecurityManager());
		File f = new File("d:/data/a.txt");
		FileInputStream fin = new FileInputStream(f);
		System.out.println(fin.read());
		fin.close();
		
		Object obj = new String();
		
		
	}

}

```



# 反射

获得运行时**类的信息**的一种技术.

JAVA反射机制是在运行状态中，

对于任意一个类，都能够知道这个类的所有属性和方法；

对于任意一个对象，都能够调用它的任意一个方法和属性；

反射就是把java类中的各种成分映射成一个个的Java对象 

这种动态获取的信息以及动态调用对象的方法的功能称为java语言的反射机制。 

> **使用的前提条件：必须先得到代表的字节码的Class，Class类用于表示.class文件（字节码** 

从字节码文件中获得信息

```java
// 获取对象的类型
Object s = new String();
System.out.println(s.getClass().getName());// java.lang.String
```

## 获得字节码文件对象

1. `Class<Person> c = Person.class;`
2. `Class<? extends Person> c = new Person().getClass();`
3. `Class<?> c  = Class.forNmae("完全命名包名.类名")`

## 方法

1. getDeclaredFileds();获得所有的属性(包含私有的)
2. getDeclaredMethods(）获得所有方法
3.  getDeclaredConstructors();  获得所有构造方法
4. newInstance():获得一个实例

```java
class Person{
    private int no;
    public String name;
    Person(){
        System.out.println("无参构造");
    }
    Person(int no,String name){
        this.no= no ;
        this.name = name;
        System.out.println("带参数的构造："+ this.no + ":" + this.name);
    }
    public void f() {
        System.out.println("无参 f");
    }
    public String sf(String str , int num) {
        return "字符串:" + str + num;
    }
}
```

```java
public static void main(String[] args) throws Exception {
    //从 Person.class中获得信息： 反射
    //获得 字节码文件 对应的对象
    //1.
    // Class<Person> c = Person.class;
    //2.
    // Class<? extends Person> c = new Person().getClass();
    //3 获得 字节码文件 对应的对象
    Class<?> c = Class.forName("day18.loader.Person");
}
```

## 获得字节码文件的属性(Field)

> getDeclaredFileds();获得所有的属性(包含私有的)

### 常用方法:

1. getName():  名字
2. getType():  类型
3. getModifiers():  访问权限
4. 属性.get(实例),-----实例.属性

```java
//----------获得所有的属性-------------------
//		Field [] fs = c.getFields();//公共的
		Field [] fs = c.getDeclaredFields();
		for(Field f :fs) {
			System.out.println(f.getName());//名
			System.out.println(f.getType());//数据类型
			System.out.println(Modifier.toString(f.getModifiers()));// 访问权限
		}
		Field f = c.getDeclaredField("no");
//		System.setSecurityManager(new SecurityManager());
		f.setAccessible(true);//
		Object obj = c.newInstance();//获得一个实例对象
		f.set(obj, 22);
		System.out.println(f.get(obj));
```

## 获得方法(Method)

> getDeclaredMethods();  获取所有方法

### 常用方法

1. getReturnType():	获取返回值得类型
2. getParameterTyoes(): 获得参数列表,数组[]
3. 方法.invoke(实例,[参数])--------实例.方法()

```java
//---------------方法------------------
Method [] ms = c.getDeclaredMethods();
for(Method m : ms) {
    System.out.println(m.getName());
    System.out.println(Modifier.toString(m.getModifiers()));
    System.out.println(m.getReturnType());//返回值类型
    System.out.println(Arrays.toString(m.getParameterTypes()));//获得 参数列表

}
//访问单个方法
Method m1 = c.getDeclaredMethod("f" );
Object obj = c.newInstance();//创建一个实例
m1.invoke(obj);//调用方法
//
Method m2 = c.getDeclaredMethod("sf", String.class,int.class);
String s = (String)m2.invoke(obj, "hello",345);
System.out.println(s);
```

## 获得构造方法

> getDeclaredConstructors();  获得所有构造方法

### 方法

1. getParameterTypes();获取参数类型
2. newInstance([参数...]):调用构造

```java
//---------构造---------------

Constructor [] crs = c.getDeclaredConstructors();
for(Constructor cr : crs ) {
    System.out.println(Arrays.toString(cr.getParameterTypes()));
}
//单个构造
Constructor cr1 = c.getDeclaredConstructor();
cr1.newInstance();
Constructor cr2 = c.getDeclaredConstructor(int.class,String.class);
cr2.newInstance(111,"abc");
```



# 基本注解

1. @override
2. @Depercated:说此方法已过时 
3. @SupperessWarnings:镇压警告:不出现黄色警告线,遇到错误照样报错
   1. all:所有的
   2. unused:变量声明没有使用
   3. rawtypes:原生类型
   4. unchecked: 类型检查
   5. serial: 版本号(序列化的类最好添加版本号)
4. @FunctionalInterface:函数式接口

```java
//                版本号
//@SuppressWarnings("serial")
//               所有的
@SuppressWarnings("all")
class DemoNew implements Serializable{

}
class Base{
    public void af() {}
    //已过时
    @Deprecated
    public void f() {}
    //  类型检查
    @SuppressWarnings("unchecked")
    public void sf() {
        //镇压警告
        //                 变量声明了 没有使用
        @SuppressWarnings("unused")
        int n ;
        //                      原生类型
        @SuppressWarnings({ "rawtypes", "unused" })
        List list = new ArrayList();
        list.add("abc");
        list.add(111);
    }
}
class Sub extends Base{
    @Override
    public void af() {}
}

@FunctionalInterface
interface Info{
    void af();
}
public class TestDemo7 {

    public static void main(String[] args) {
        Base base = new Base();
        base.f();

    }

}

```


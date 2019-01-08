---
title: java基础day17
tags:
  - 缓冲流
  - 字符流
  - 多线程
  - 同步
date: 2018-08-20 08:49:10
categories:
top:

---

# 字节流

1. 读InputStream
2. 写OutputStream
3. 节点流:FileInputStream
4. 缓冲流:BufferedInputStream
5. 序列化(读)ObjectInputStream
6. 反序列化(写):ObjectOutputStream
7. 打印输出字节流PrintStream

<!-- more -->

1. 使用步骤
   1. 创建流对象
   2. 读/写
   3. 关闭流
2. 构造方法
   1. FileInputStream(): 默认构造,覆盖
   2. FileInputStream(File,boolean):true:追加,默认false
3. 节点流小案例

复制图片: 使用字节流,不能使用字符流

```java
public static void main(String[] args) {
    File file = new File("C:\\Users\\sun\\Desktop\\day17", "壁纸4.jpg");
    File newFile = new File("C:\\Users\\sun\\Desktop\\day17", "copy.jpg");

    if (file.exists()) {
        try {
            // 1.创建流对象
            FileInputStream fis = new FileInputStream(file);
            FileOutputStream fos = new FileOutputStream(newFile);

            // 2. 读取图片到byte[] 
            byte[] bs = new byte[fis.available()];
            fis.read(bs);

            //3. 写文件
            fos.write(bs);
            if(newFile.exists()&&file.length()==newFile.length()){
                System.out.println("复制成功!!!");
            }


        } catch (Exception e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }

    } else {
        System.out.println("文件不存在,复制失败!!!");
    }
}
```

## 缓冲流

1. 默认8192字节.
2. BufferedInputStream();包装流,处理流
3. BufferedOutputStreaam:输出缓冲流

```java
//将控制台的输入写入到文件中
public static void main(String[] args) throws Exception {
		// 创建输出流
		File file = new File("C:\\Users\\sun\\Desktop\\day17", "name.txt");
		BufferedOutputStream bos = new BufferedOutputStream(new FileOutputStream(file));
		// 录入信心
		boolean flag = true;
		Scanner sc = new Scanner(System.in);
		while (flag) {
			System.out.println("请输入学员信息:");
			String name = sc.nextLine();
			if ("q".equals(name)) {
				flag = false;
				System.out.println("输入结束!!!");
			} else {
				bos.write(name.getBytes());
				bos.write("\r\n".getBytes());
			}

		}
		bos.close();
		System.out.println("文件保存路径:" + file.getAbsolutePath());
	}
```

## Scanner类

`Scanner sc = new Scanner(System.in)`

System类:

  `public final static InputStream in = null;`

### 常用方法

1. next(): 读取数据
   1. next:不会获取空格
   2. nextLine():获取空格
2. hasNext():判读数据类型,通过next()可以读取数据.
3. nextXxx()、hasNextXxx()等等

```java
public static void main(String[] args) throws Exception {
    File file = new File("C:\\Users\\sun\\Desktop\\day17", "name.txt");
    FileInputStream fis = new FileInputStream(file);
    Scanner sc = new Scanner(fis);
    String str = sc.next();
    System.out.println(str);

    Scanner sc2 = new Scanner("aa bb cc");
    String str2 = sc2.next();
    System.out.println(str2);//aa
    System.out.println(sc2.nextLine());// bb cc

}
```

## 数据流

数据输入输出流：`DataInputStream`(`FileInputStream`)数据输入流 

数据输出流——用于读写基本数据类型：

规则：先进先出【先存进去先读出来，读出类型与写入类型一致】 



```java
public static void main(String[] args) throws Exception {
    File file = new File("C:\\Users\\sun\\Desktop\\day17", "name.txt");
    FileInputStream fis = new FileInputStream(file);
    DataInputStream dataInputStream = new DataInputStream(fis);
    FileOutputStream fos = new FileOutputStream(file);
    DataOutputStream dataOutputStream = new DataOutputStream(fos);

    // 写writeInt,wrriteByte....writeUTF()
    int[] no = new int[] { 11, 22, 33 };
    String[] name = new String[] { "I", "love", "you" };
    for (int i = 0; i < no.length; i++) {
        dataOutputStream.writeInt(no[i]);
        dataOutputStream.writeUTF(name[i]);
    }


    //读,按照写入的顺序读
    int i;
    for (i = 0; i < no.length; i++) {
        System.out.print(dataInputStream.readInt() + " ");
        System.out.print(dataInputStream.readUTF() + " ");

    }

    //关闭流
    dataInputStream.close();
    dataOutputStream.close();
}
```



## 对象流

对象流:

1. 序列化(读)ObjectInputStream
2. 反序列化(写):ObjectOutputStream

### 序列化前提

对象要序列化前提:	类要实现 `Serializable接口`

### 序列化

把对象转换成字节序列(把对象存到磁盘文件中)

### 反序列化

读,把文件的内容读出来(把字符序列还原成对象)

- 注意:
- 版本号:当反序列化的时候,原来的类可能会有修改,这时候版本号随机不一致,反序列化会失败
- 解决方法:
  - 手动给定序列化
     `	private static final long serialVersionUID = 1L;`

```java
public class Student implements Serializable {
    private static final long serialVersionUID = 1L;
    private String name;
    private int age;
    private int id;

    public Student() {
        super();
        // TODO Auto-generated constructor stub
    }

    public Student(String name, int age) {
        super();
        this.name = name;
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    @Override
    public String toString() {
        return "Student [name=" + name + ", age=" + age + "]";
    }

}

```

```java
public static void main(String[] args) throws Exception {
    // 1.创建对象流的对象
    File file = new File("C:\\Users\\sun\\Desktop\\day17", "object.txt");
    FileOutputStream fos = new FileOutputStream(file);
    ObjectOutputStream outputStream = new ObjectOutputStream(fos);

    //2. 创建对象
    Student stu1 = new Student("刘备", 33);

    // 3.将对象序列化,写
    outputStream.writeObject(stu1);

    // 4. 反序列化,读
    FileInputStream fis = new FileInputStream(file);
    ObjectInputStream inputStream = new ObjectInputStream(fis);

    Object readObject = inputStream.readObject();
    Student student = (Student) readObject;
    System.out.println(student);
}
```



## 打印流

```java
public static void main(String[] args) throws Exception {
    File file = new File("C:\\Users\\sun\\Desktop\\day17", "print.txt");
    if(!file.exists()){
        file.createNewFile();
    }
    //读
    FileInputStream fis = new FileInputStream(file);
    byte[] bs = new byte[fis.available()];
    fis.read(bs);
    fis.close();

    //打印到控制台
    PrintStream printStream = new PrintStream(System.out);
    printStream.println(new String(bs ,0 ,bs.length,"UTF-8"));
    printStream.close();

    //打印到文件中
    PrintStream printStream2 = new PrintStream("C:\\Users\\sun\\Desktop\\day17\\print2.txt");
    printStream2.print(new String(bs ,0 ,bs.length,"UTF-8"));
    printStream2.close();
}
```



# 字符流

字符流只针对文本

|        |                    |            |
| ------ | ------------------ | ---------- |
| Reader | BufferedReader     |            |
| Reader | InputStreamReader  | FileReader |
| Writer | BufferedWriter     |            |
| Writer | OutputStreamWriter | FileWriter |
| Writer | PrintWriter        |            |



## 文件字符流

FileReader

FileWriter

对同一个文件操作的时候,不能同时写FileReader,FileWriter,用的时候使用对应的流

```java
//将xie.txt中的文件内容逆序写入到一个文本中
public static void main(String[] args) throws Exception {
    File file = new File("C:\\Users\\sun\\Desktop\\day17", "xie.txt");
    File destFile = new File("C:\\Users\\sun\\Desktop\\day17", "xiecopy.txt");
    if (!file.exists()) {
        file.createNewFile();
    }
    // 1.读/写
    FileReader fileReader = new FileReader(file);
    FileWriter fileWriter = new FileWriter(destFile);

    // 写
    // fileWriter.write("abc".toCharArray());
    fileWriter.write("ABCD1234");

    // 读
    StringBuffer sf = new StringBuffer();
    int temp;

    while ((temp = fileReader.read()) != -1) {

        sf.append((char)temp);
        System.out.println(sf);
    }

    //逆序后写入新的文件
    fileWriter.write(sf.reverse().toString());

    // 关闭流
    fileReader.close();
    fileWriter.close();
}
```



## 缓冲流

BufferedRead ,BufferedWriter

可以一次处理一行

1. readLine()
   1. 如果读完: 返回null

```java
public static void main(String[] args) throws IOException {
    //1
    FileReader fr = new FileReader("d:/data/a.txt");
    BufferedReader bfr = new BufferedReader(fr);
    //2
    String s ;
    while((s = bfr.readLine()) != null) {
        System.out.println(s);
    }

    //3
    fr.close();
}
```



## 打印输出字符流

相当于是`System.out.print("")`,打印输出到文件中.

```java
public static void main(String[] args) throws FileNotFoundException {
    //1.
    PrintWriter pw = new PrintWriter("d:/data/number.txt");
    //2
    for(int i = 1; i <= 5; i++) {
        pw.println("第" + i + "个数字");
        //			System.out.println("第" + i + "个数字");
    }
    //3.
    pw.close();
}
```

BufferedReader 和PrintWriter一起使用

```java
BufferedReader bfr = new BufferedReader(new InputStreamReader(System.in));
PrintWriter pw = new PrintWriter("d:/data/name.txt");
//2
String s ;
System.out.println("--input name:");
while(true) {
    s = bfr.readLine();
    if(s.equals("q")) {
        break;
    }
    pw.println(s);
}
//3
bfr.close();
pw.close();
```



# 自动释放资源

不用close,系统指定释放资源

前提:**流要继承AutoCloseable;**

```java
try（声明需要释放的资源）{

    可能产生异常的语句。

}catch(异常){

}
```

```java
try(BufferedReader bfr = new BufferedReader(new InputStreamReader(System.in));
    PrintWriter pw = new PrintWriter("d:/data/name.txt");){

    //2
    String s ;
    System.out.println("--input name:");
    while(true) {
        s = bfr.readLine();
        if(s.equals("q")) {
            break;
        }
        pw.println(s);
    }
}catch(IOException e) {
    e.printStackTrace();
}
```



# 多线程编程

1. 进程: 内存中,正在运行的一个应用程序.
2. 线程:进程中的一个执行流程
3. 多线程:进程中有二个或二个以上的执行流程

> 其实是轮训切换,速度非常快

## java程序

> 启动一个java程序,至少会启动几个线程

      		1. 主线程
      		2. 垃圾回收线程

## 线程的状态

>New(新建）
>
>​	新建一个线程对象
>
>​	Thread t = new Thread();

>Runnable(就绪）
>
>​	等待cpu调用执行

>Running(运行）
>
>​	就绪状态的线程被cpu调用

>Blocked(阻塞）
>
>​	sleep(毫秒)
>
>​		先回到就绪装填
>
>​	join()
>
>​		让其他线程先执行
>
>​	同步阻塞:
>
>​		相当于排队
>
>​	wait()阻塞
>
>​		notify()/notifyAll()唤醒,锁定池等待:
>
>​	出现了某种原因,暂停了cpu的调用执行

>Dead(死）
>
>​	线程运行结束

## 主线程

java 程序启动时,一个线程立刻运行,该线程常叫做程序的主线程(Main thread)

程序开始时,就执行

主线程是参数子线程的线程

默认名字:**main**,优先级为5

子类名字:**Thread-0**,依次递增

线程只能启动一次:

一个线程多次调用:`IllegalThreadStateException`

```java
public static void main(String[] args) {
    // 显示当前方法里的线程
    System.out.println(Thread.currentThread());// Thread[main,5,main]

    // 修改线程的名字
    Thread.currentThread().setName("abc");
    System.out.println(Thread.currentThread());//Thread[abc,5,main]
 
```

## 创建线程方式

1. 继承Thread类
2. 实现Runable接口

## Thread类

### 方法

1. CurrentThread():获取当前方法中的线程
2. Thread.currentThread().setName(""):设置线程的名字
3. interrupt():线程中断
   1. sleep()
   2. join()
   3. 只有上面二个方法才会产生中断异常
4. yield():线程让步
   1. Thread.yield()

多线程例子

### 主线程结束后，程序结束



1. 主线程等待的时间足够长sleep()
2. isLuver():当前线程是否处于运行状态，是true

2. 让其他线程先执行join()

> sleep(毫秘）等待，睡眠，
>
> 线程A处于sleep（）那么，线程A就处于阻塞状态，
>
> 当参数的时间超时了，那么会回到就绪状态等待CPU调用执行。

> 当在主线程中调用了子线程的join（）
>
> 那么主线程处于阻塞状态，等待子线程执行完成后，进入就绪状态，再恢复执行
>

## 线程修改名字

1. 继承Thread类
   - 重写该类的构造方法,传入一个name

```java
class MyThread extends Thread {
    public MyThread() {
        super();
    }

    public MyThread(String name) {
        super(name);
        // TODO Auto-generated constructor stub
    }

    @Override
    public void run() {
        System.out.println(Thread.currentThread().getName() + ":hello");
    }
}

public class Thread2 {
    public static void main(String[] args) {
        // 新建一个线程
        MyThread thread1 = new MyThread("A");
        MyThread thread2 = new MyThread("B");

        // 启动线程,进入就绪状态
        // start()的目的是调用run()方法;
        // 直接调用run()方法是主线程调用,与子线程无关
        thread1.start();
        thread2.start();

    }
}

//---------
A:hello
B:hello
```

1. 实现runable接口

   - 在传给Thread对象的后面添加name

     ` Thread t1 = new Thread(myThreadClass,name");`

```java
// 这里不是一个线程类
class MyThread2 implements Runnable {

    @Override
    public void run() {
        System.out.println(Thread.currentThread().getName()+":hello");
    }

}

public class ThreadRunableDemo {
    public static void main(String[] args) {
        // 1. 新建实现接口的对象
        // 这不是一个线程对象
        MyThread2 my = new MyThread2();

        // 2. 需要将实行了接口的对象传到Thread对象
        Thread t1 = new Thread(my,"C");
        t1.start();
    }
}

//-----------
C:hello
```



## 线程的优先级

> java 中的优先级1-10,1最低, 10 最高
>
> 优先级高,cpu调用的次数多
>
> 并不是绝对,优先级需要看系统等

```java
public static void main(String[] args) {
    ThreadDemo1 demo = new ThreadDemo1();
    Thread t1 = new Thread(demo,"t1");
    Thread t2 = new Thread(demo,"t2");
    //设置优先级
    //	t1.setPriority(10);
    //	t2.setPriority(1);
    
    //推荐下面几种
    t1.setPriority(Thread.MAX_PRIORITY);
    t2.setPriority(Thread.MIN_PRIORITY);
    //	t1.setPriority(Thread.NORM_PRIORITY);

    t1.start();
    t2.start();

}
```



## 线程中断

当线程A调用了线程B的线程中断方法，那么

线程B暂停cpu的调用执行进入异常处理，

然后继续执行。

**前提:线程b处于sleep()或join()**

```java
class ThreadDemo2 implements Runnable{

    @Override
    public void run() {
        for(int i = 1; i <= 10; i++) {
            System.out.println(Thread.currentThread().getName() + ":" + i);
            if(i == 5) {
                try {
                    //	Thread.sleep(1000);
                    Thread.currentThread().join();
                } catch (InterruptedException e) {
                    System.out.println("进入异常处理");
                }
            }
        }
    }

}
public class TestIerrupt {
    public static void main(String[] args) {
        ThreadDemo2 demo = new ThreadDemo2();
        Thread t1 = new Thread(demo,"t1");
        Thread t2 = new Thread(demo,"t2");
        t1.start();
        t2.start();
        t1.interrupt();//中断t1,调用中断异常处理,没有join()和sleep();没有效果
    }
}

```



## 线程让步

理论上:

​	线程A让步给 同等优先级的线程B或级别比它高的线程B

实际上:让步后成为就绪状态,等待cpu的调用执行,(并不是真正的让步)

```java
class ThreadDemo3 implements Runnable{

    @Override
    public void run() {
        for(int i = 1; i <= 10; i++) {
            System.out.println(Thread.currentThread().getName() + ":" + i);
            if(i == 5 && Thread.currentThread().getName().equals("t1")) {
                Thread.yield();//让步
            }
        }
    }

}
public class TestYield {

    public static void main(String[] args) {
        //让步
        ThreadDemo3 demo = new ThreadDemo3();
        Thread t1 = new Thread(demo,"t1");
        Thread t2 = new Thread(demo,"t2");
        t1.start();
        t2.start();
    }

}
```



## 同步

1. 使用同步代码块,可以指定锁的对象

   ​	` synchronized (this) {  //调用的功能  }`

2. 使用同步方法,锁的是this

   ​	`synchronized 方法() `

> 锁的都是对象,其他对象在锁定池中等着,出来后处于就绪状态

```java
class Bank implements Runnable {
    private int money;

    // synchronized public void setmoney(){
    public void setmoney() {
        this.money += 100;
        System.out.println("剩余:" + Thread.currentThread().getName() + ":" + money);
    }

    @Override
    public void run() {
        for (int i = 0; i < 3; i++) {
            synchronized (this) {
                setmoney();
            }
        }
    }
}
```

### 加锁

当线程A 执行到了同步代码块或同步方法的代码的时候,要获得此对象的同步锁,获得成功,那么,其他线程B进入到锁定池中处于锁定状态

### 释放锁

1. 当线程A 正常执行完同步代码块或同步方法中的代码时.
2. 当代码块 或 方法中出现没有解决的Error或Exception时
3. 当代码块或方法中return,break时
4. wait().

# 创建子线程两种方式 的区别：

  1.继承Thread可以 继承到 Thread的所有的方法；
  2.实现接口 只有一个run（）方法；
    更有利于资源共享。
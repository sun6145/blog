---
title: java基础day16
tags:
  - null
  - null
date: 2018-08-17 09:30:35
categories:
top:
---


# File
File类主要用来获取文件（或目录）本身的一些信息，如文件的名字，不涉及文件的读写操作。
文件操作的宝都在`java.io.*`包下
文件目录的分隔:`\\`或反斜杠(/)



<!-- more -->

## file方法
1. exists（)
2. createNewFile()
3. getName()
4. getPath()
5. getAbsolutePath()
6. getParent()
7. canRead()
8. canWrite()
9. isFile()
10. long lastModified()
11. long length()
12. boolean delete()

```java
public static void main(String[] args) throws Exception {
    // 构造方法
    //默认为当前项目
    File file = new File("a.txt");
    File file2 = new File("d:/a/a.txt");
    File file3 = new File("d:/a", "a.txt");

    // 判断文件是否存在
    System.out.println(file.exists());

    // 新建文件
    file.createNewFile();
    System.out.println("---------"+file.exists());
    // 获得文件的名称
    System.out.println(file.getName());

    // 获得文件路径路径
    // 路径，如果是绝对路径显示绝对，如果是相对路径显示相对
    System.out.println(file.getPath());
    // 绝对路径
    System.out.println(file.getAbsolutePath());

    //获取父目录
    System.out.println(file.getParent());

    //是否可读,可写
    System.out.println(file.canRead());
    System.out.println(file.canWrite());

    //是不是文件
    System.out.println(file.isFile());

    //上一次修改时间

    System.out.println(new Date(file.lastModified()));

    //文件大小
    System.out.println(file.length());	//0

    //删除文件
    System.out.println(file.delete());
}
```



# 目录
目录是一个包含其他文件和路径列表的File类对象


## 目录方法
- 判断方法：

1. boolean isDirectory():判断file是不是目录
2. boolean exists()： 判断目录是否存在

- 获得方法：

1. String[] list()： 获取目录下的子目录和文件的字符串形式
2. File[] listFiles(): 获取目录下的子目录和文件的file形式
3. String[] list(FilenameFilter): 对文件名的过滤
4. File[] listFiles(FileFilter):对File 对象进行过滤

- 创建目录

1. mkdir(): 不会创建父目录
2. mkdirs(): 不目录不存在,会创建父目录

```java
File file = new File("C:\\Users\\sun\\Desktop\\day15");
System.out.println(file.getAbsolutePath());

// 判断是否是文件夹
System.out.println(file.isDirectory());

// 判断文件夹存在
System.out.println(file.exists());

// 获得目录下的子目录 和 文件的字符串形式
file.list(new FilenameFilter() {

    @Override
    public boolean accept(File dir, String name) {
        return name.endsWith(".java");
    }
});

// lambda表达式
String[] fileList = file.list((d, name) -> name.endsWith("java"));

//遍历输出
for (String str : fileList) {
    System.out.println(str);
}

// 获得目录下的子目录 和 文件的File形式

File[] listFiles = file.listFiles();
listFiles = file.listFiles(new FileFilter() {

    @Override
    public boolean accept(File path) {
        return path.getName().endsWith("java");
    }
});

// 表达式
listFiles = file.listFiles(f -> f.getName().endsWith("java"));

// 查看
for (File ff : listFiles) {
    if (ff.isDirectory()) {
        System.out.println(ff.getPath());
    } else {
        System.out.println("文件:" + ff.getName());
    }

}
```

## 递归遍历文件夹

```java
public static void main(String[] args) {
    File file = new File("C:\\Users\\sun\\Desktop\\day15");

    // 递归遍历文件
    while (true) {
        File[] listFiles = file.listFiles();
        if (listFiles == null) {
            return;
        }

        for (File file1 : listFiles) {
            if (file1.isDirectory()) {
                System.out.println(file1.getPath());
            } else {
                System.out.println(file1.getAbsolutePath());
            }
            file = file1;
        }
    }

}
```



# 流
> Java在java.io包定义多个流类型来实现输入和输出
>
> java中,文件的输入输出功能通过流实现
>
> 流:可以理解成为一组有顺序的、有起点、有终点的动态数据集合

## 流的分类

1. 按照流的数据类型：分为字节流和字符流

​	英文:	1字节
	中文:	2字节

2. 按照方向：输入和输出

​	输入: 硬盘 ----> 内存

​	输出: 内存 ----> 硬盘

3. 按照功能：节点流和处理流(过滤流,包装流)

​	节点流: 直接对数据源 进行 读写
	处理流: 在节点流的基础上套接一个流
			功能更多
			效率更高

# 字节流(Stream)
InputStream
> 所有字节输入流 的父类

- InputStream

  1. FileInputStream
  2. FilterInputStream
     1. DataInputStream
     2. BufferedInputStream
  3. ObjectInputStream

- OutputStream

  1. FileOutputStream

  2. FilterOutPutStream

     1. DataOutputStream
     2. BufferedOutputSteam
     3. PrintStream

  3. ObjectOutputStream

     

## 方法

> 读时，路径一定要存在
> 写的时候文件可以自动创建文件。
1. InputStream
	1. read()
2. OutputStream
	2. write()
3. close(): 关闭流对象

## 使用

> 步骤：
>
> 1. 创建流对象
> 2. 该读写就读写
> 3. 关闭流

### 文件流
#### FileInputStream

```java
public static void main(String[] args) throws Exception {
    File file = new File("C:\\Users\\sun\\Desktop\\day16\\a.txt");
    if (!file.exists()) {
        file.createNewFile();
    }

    // 1. 创建流对象
    // 读取文件Inputstream,可能出现“文件找不到异常”
    FileInputStream fis = new FileInputStream(file);
    FileOutputStream fos = new FileOutputStream(file);

    // 2. 读/写
    // 写
    String str = "Hello.";
    byte[] bytes = str.getBytes();
    fos.write(bytes);

    //读
    int i;
    while ((i = fis.read()) != -1) {
        System.out.print((char) i);
    }

    // 3. 关闭流对象
    fos.close();
    fis.close();
}
```

#### 读取中文乱码问题

解决办法：

1. 字符流读取read()

```java
// 创建读/写的字节流对象
FileInputStream fis = new FileInputStream(file);
FileOutputStream fos = new FileOutputStream(file);
// 包装流:字节--->字符流
InputStreamReader reader = new InputStreamReader(fis);

// 字符流读取
int temp;
while ((temp = reader.read()) != -1) {
    System.out.println((char) temp);
}
```

2. 字节流读取到数组,再显示read(byte[]): 将流中的数据读取到byte数组中，返回长度，在内存中读到数组中，效率高

```java
// 拿一个袋子（byte[]）
// 文件的长度作为数组的长度
// byte[] cbuf = new byte[(int) file.length()];

// 流读取的长度作为数组的长度
byte[] cbuf = new byte[fis.available()];

// 将流中的数据读取到字节数组中
System.out.println(fis.read(cbuf));

// read(字节数组,字节数组的起始位置,存几个)
fis.read(cbuf,4,2);

// 从袋子里拿出来(转为字符)
// System.out.println(new String(cbuf,"UTF-8"));
System.out.println(new String(cbuf,"GBK"));
```

> 字节流read(数组，数组起始位置，存的个数)
>
> 字节流write(数组，数组起始位置，存的个数)
>
> **起始位置 + 存的个数之和** 要小于数组的长度，否者出现数组下标越界的异常

#### 文件的复制

1. FileOutputStream fos = new FileOutputStream(destFile,false);//默认覆盖
2. FileOutputStream fos2 = new FileOutputStream(destFile,true);//追加

```java
public static void main(String[] args) throws Exception {
    File file = new File("C:\\Users\\sun\\Desktop\\day16\\b.txt");
    File destFile = new File("C:\\Users\\sun\\Desktop\\day16\\c.txt");


    // 1. 创建流对象
    // 读取文件Inputstream,文件找不到异常
    FileInputStream fis = new FileInputStream(file);
    FileOutputStream fos = new FileOutputStream(destFile,false);//默认覆盖
   
    // 包装流:字节--->字符流
    InputStreamReader reader = new InputStreamReader(fis);
    // 2. 读/写

    //将b.txt 复制到c.txt
    byte[] b = new byte[fis.available()];
    fis.read(b);

    //写
    System.out.println("1233:"+b.length);
    fos.write(b, 0, b.length);
    //	fos.write(b);
    System.out.println("success");

    // 3. 关闭流对象
    fos.close();
    fis.close();
}
```


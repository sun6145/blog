---
title: Linuxday02
tags:
  - null
  - null
date: 2018-09-14 09:29:17
categories:
top:
---


# VI编辑器

> 操作系统内置的文本编辑器,vim是增强版本

## 使用

>  vi/vim 文件

## 三种模式

1. 命令模式
   1. vi 文件 进入命令模式
   2. a/i/o   进入插入模式

2. 编辑模式/插入模式
   1. esc 进入命令模式
3. 底行模式
   1. `:命令`
      - q:退出
      - w:保存
      - q!:强制退出
      - wq!:强制保存退出

## 命令模式下常用命令

<!-- more -->

### 光标的移动

> 字符移动

1. 上下左右键,一个一个字符移动
2. hjkl:左/下/上/右

> 单词移动

1. w:移动到下一个单词首字母(包含标点)
2. W:移动到下一个单词首字母(不包含标点)
3. b:移动到上一个单词首字母(包含标点)
4. B:移动到上一个单词首字母(不包含标点)
5. e/E移动到下一个单尾字母/(包含标点)

> 逐句移动
>

1. (:移动到句子的开头
2. ):移动到句子的结尾

> 段落

1. {:移动到段落的开头
2. }:移动到段落的结尾

> 开始:G  结束gg

> M:移动到屏幕的中央

## 编辑模式

> a:光标下一位
>
> A:光标的行尾
>
> i:光标之前
>
> I:光标行的头
>
> o:光标下一行
>
> O:在光标这一行(当前行挤下去)
>
> yy:复制
>
> ​	3yy:从当前行复制3行
>
> dd:删除
>
> ​	3dd:从当前行删除3行
>
> p:粘贴
>
> u:撤销

## 底行模式

> :set nu    显示行号
>
> :set nonu 不显示行号
>
> :/查找内容
>
> ​	n:下一个
>
> ​	N:上一个
>
> :noh  取消高亮
>
> :N  跳转到N行



> :开始行,结束行 s/被替换的诗句/替换的内容
>
> ​	:1 s/sad/ddd(/g)   将第一行的sad替换为ddd  (不带,第一个,带g所有替换)
>
> .代表当前光标行
>
> $ 缓冲区的最后一行





# 网络管理

## ifconfig(centos 6)

> centos 7  :ip addr
>
> lo: 回环地址

- -a	 显示所有的网络接口(默认活动的)
- ifconfig 网卡名 up/down   开启/关闭网络接口



## ping 

> 查看网络的连接信息



## 虚拟机和宿主机通讯方式

1. bridged(桥接模式:访问外网)

   > 主机的网卡和虚拟交换机(**vmnet0**)之间通过虚拟网桥连接
   >
   > 主机和虚拟机的ip是在同一个网段
   >
   > 缺点:不利于搭建大型集群

2. NAT模式(可以**vmnet8**局域网和外网):

   > 虚拟系统借助NAT(网络地址转换)功能,通过宿主机的
   >
   > 宿主机创建vet8的虚拟网卡,虚拟机交换机用(vmnet8)建立连接,局域网
   >
   > NAT地址转化,连接虚拟交换机和网卡,访问外网
   >
   > DHCP协议:动态地址分配

3. 仅主机模式,没有NAT设备(访问局域网)(**vmnet1**)



## 设置静态ip

### 图形界面

> 设置虚拟网络编辑器,设置vmnet8的子网Ip 192.168.2.0
>
> 点击NAT设置,找到网关:192168.2.2
>
> windows系统下vmnet8的ip地址192.168.2.1
>
> network connections -eth0 -ipv4setting-manual -add
>
> 添加 192.168.2.101 -255.255.255-192.168.2.2
>
> DNS:网关地址192.168.2.2
>
> service network restart重启网络服务



### 命令方式

> ` vim /etc/sysconfig/network-scripts/ifcfg-eth0 `

```txt
DEVICE=eth0
TYPE=Ethernet
UUID=8543ec50-f2b0-43c6-956e-5c059c1f83b6
ONBOOT=yes
NM_CONTROLLED=yes
BOOTPROTO=none
DEFROUTE=yes
IPV4_FAILURE_FATAL=yes
IPV6INIT=no
NAME="System eth0"
HWADDR=00:0C:29:7C:77:1B
IPADDR=192.168.2.101
PREFIX=24
GATEWAY=192.168.2.2
DNS1=192.168.2.2
LAST_CONNECT=1536895478
NETMASK=255.255.255.0

```

> `service network restart`

# 修改主机名

1. hostname: 查看和修改主机名称

   > hostname	查看主机名
   >
   > hostname  主机名(临时有效,重启后失效)

2. 永久修改(重启生效)

   > `vim /etc/sysconfig/network`

   ```
   NETWORKING=yes
   HOSTNAME=hdp01
   ```

   重启: reboot

# 修改主机名与ip映射

> 域名和ip地址的映射

- windows C:\Windows\System32\drivers\etc

- linux: 

  - `vi /etc/hosts`
  - 添加

  ```
  
  127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
  ::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
  192.168.2.101 hdp1
  
  ```

  - 测试:ping hdp1



# 其他常用命令

1. host :域名解析

   > host www.bai.du.com

2. netstat :查看网络接口

   - `netstat -nltp`

   1. `kill -9 pid`

# 防火墙:控制网络访问"出入"状态

> /ect/sysconfig/iptables

- 查看防护墙状态: `service iptables status`

- 关闭防火墙:`service iptables stop`(重启自动打开)

- `start/restart`

- `chkconfig --list`:查看服务的开机自启状态

- `chkconfig --list|grep iptables`:查看iptables的启动管理

- `chkconfig iptables on/off`:开机启动/关闭

- `vim /etc/sysconfig/iptables下添加规则`

- `iptables -A INPUT -p tcp --dport22 -j DROP`禁止SSH登陆

- `iptables -D INPUT -p tcp --dport22 -j DROP`:删除规则

- `iptables -I INPUT -p tcp --dport 80 -j accept`:添加一条规则,开放80端口



4. 开启http服务

   >  service httpd status  查看状态
   >
   > service httpd start  开启(临时开启)
   >
   > chkconfig --list|grep httpd
   >
   > chkconfig httpd on

# 系统的启动级别

> `runlevel`:查看当前的系统启动级别(0-6)
>
> 0:关机
>
> 1:单用户
>
> 2:多用户,不能访问网络
>
> 3:多用户,能访问网络(全功能,不带图形界面,速度快)
>
> 4:未使用
>
> 5:带有图形界面系统模式
>
> 6.重启

**修改启动级别**

> vim /etc/inittab
>
> id:3:initdefault:
>
> reboot:重启



# SecureCRT-8.1.4

**设置:**

1. option -- global options --default Session  -- edit default session
2. Terminal--Enulation--Terminal ---> "Linux",勾选ANSI color,勾选Use color Scheme
3. Appearance--->Current color scheme  设置主题和字体
4. character encoding **utf-8**
5. ok 应用到所有

操作:

1. 复制: 按住鼠标左键,选择文本
2. 粘贴:右键即可

# 用户,组

> 用户:能够获取系统权限集合
>
> 组:权限的容器



## ll查询的信息

-rw-r--r--. 1 root root    13 Sep 14 09:43 a.txt
drwxr-xr-x. 2 root root  4096 Sep 14 10:09 Desktop

权限   链接数(文件链接数1 目录:子目录个数) 

root :所属用户

root: 所属用户组

4096: 文件大小

Sep 14 10:09:修改日期

 Desktop 文件名称



## 用户类型

- 管理员root 
  - 具有使用所有权限的用户,uid为0
- 系统用户
  - 保障系统运行的用户,一般不提供密码登录系统uid(1-499之间)
- 普通用户
  - 一般用户,使用系统的权限受限,uid(500~60000之间)



## 查看用户信息

> cat /etc/passwd

```
root:x:0:0:root:/root:/bin/bash
root: 用户名称
x: 密码的占位符	密码信息:/etc/gshadow
0: 用户id
0: 用户组id
root: 备注信息
/root 家目录
/bin/bash: shell 解释器类型

新建从500开始:
hdp01:x:500:500:hdp01:/home/hdp01:/bin/bash
```

## 操作用户

### 添加用户

> `useradd`用户名

### 修改用户

> `usermod` [选项] 用户名
>
> ​	-l	修改名称
>
> ​	-G	修改组 
>
> ​	-c	修改备注信息

### 删除用户

> `userdel -r`  用户名

### 修改密码

>  `passwd `用户名

```
cat /etc/passwd
cat /etc/group
useradd sun
usermod -G hdp01 sun		hdp01:x:500:sun
usermod -c beizu sun		sun:x:501:501:beizu:/home/sun:/bin/bash
usermod -l sun1 sun			sun1:x:501:501:beizu:/home/sun:/bin/bash
userdel sun1	//删除后创建的sun1组还存在 
```







## 用户组类型

- 系统组
  - 一般加入一些系统用户
- 普通用户组
  - 可以加入多个用户
- 私有组/基本组
  - 创建用户的时候,没有指明所属的组,就为该用户设一个与用户名同名的私有组,该私有组添加了用户会变成普通组

### 查看用户组

```
hdp01:x:500:sun

hdp01: 用户组名称
x: 组密码的占位符
500: 组id
sun 组中的用户

```

## 操作用户组

- 创建用户组: `groupadd`

- 修改用户组: `groupmod -n`:(修改名称)

- 删除用户组:`groupdel`

- 向组中添加删除用户:` gpasswd -a`:添加   `-d`:删除

```
groupadd test
useradd test1
gpasswd -a test1 test
gpasswd -d test1 test
groupdel test1
groupdel test
usredel test1
```



## 为用户配置sudoer权限

> 给普通用户赋予更大的权限做一次操作

用root编辑`/etc/sudoers`文件

```
root  ALL=(ALL) ALL
用户名 ALL=(ALL) ALL
```





## 切换用户

su 用户名



# 文件权限

> d rwx r-x r-x      （也可以用二进制表示  111 101 101  -->  755）

> 数字 4 、2 和 1表示读、写、执行权限
>
> d：标识节点类型（d：文件夹   -：文件  l：链接）
>
> r：可读   w：可写    x：可执行 

|       | **文件**             | **文件夹**                       |
| ----- | -------------------- | -------------------------------- |
| **r** | **可读取内容**       | **可以ls**                       |
| **w** | **可修改文件的内容** | **可以在其中创建或者删除子节点** |
| **x** | **能否运行这个文件** | **能否cd进入这个目录**           |

u第一组rwx： ## 表示这个文件的拥有者对它的权限：可读可写可执行

g第二组r-x：  ## 表示这个文件的所属组用户对它的权限：可读，不可写，可执行

o第三组r-x：  ## 表示这个文件的其他用户（相对于上面两类用户）对它的权限：可读，不可写，可执行



## 修改文件的权限

chmod g-rw haha.dat		## 表示将haha.dat对所属组的rw权限取消

chmod o-rw haha.dat		## 表示将haha.dat对其他人的rw权限取消

chmod u+x haha.dat		## 表示将haha.dat对所属用户的权限增加x

chmod a-x haha.dat        ## 表示将haha.dat对所用户取消x权限

|       |      |         |       |              |
| ----- | ---- | ------- | ----- | ------------ |
|       | u    | +(加入) | r/w/x | 文件的拥有者 |
| chmod | g    | -(除去) | r/w/x | 所属组用户   |
|       | o    | =(设定) | r/w/x | 其他用户     |
|       | a    |         |       | 所用户       |

## 修改文件所有权

chown angela  aaa		## 改变所属用户

chown :angela  aaa		## 改变所属组

chown angela:angela aaa/	## 同时修改所属用户和所属组



**对于文件夹的X**

1. 如果对文件夹有rw的权限:**ls的时候,仅仅能知道目录下有文件,看不到详细信息**
2. 没有x权限:**不能进入文件夹**
3. 有rwx权限的文件夹可以删除目录下的所有文件(没有对文件的权限也可以)

# 压缩和打包

z：gzip，通过gzip格式压缩或者解压缩

c：create，创建压缩文件

x：extract，解压缩文件，或者叫还原文件

v：verbose，显示过程

f：file，指定文件

t：list，列出文件

j：支持bzip2压缩和解压缩



## gzip压缩

> gzip test.txt
>
> 压缩test.txt---> test.txt.gz,不会多出文件

## gzip解压缩

>  gzip -d test.txt.gz 

## bzip2压缩

>  bzip2 test.txt
>
> 压缩test.txt --> test.txt.bz2,不会多出文件

## bzip2解压缩

> bzip2 -d test.txt.bz2 
>
> 或者 bunzip2 test.txt.bz2

## 打包

>  tar **-cvf** **test.txt.tar** test.txt 
>
> 将test.txt 打包为test.txt.tar ,会多出一个test.txt.tar文件

## 追加打包

> tar -rvf **test.txt.tar** test2.txt
>
> 表示将est2.txt文件追加到**test.txt.tar**当中

## 解包

> tar -xvf **test.txt.tar**

## 打包并压缩

> tar -**zcvf** ma.tar.gz /root/test

## 解包并解压缩

> tar -**zxvf**  ma.tar.gz 

## 解包解压缩到指定的路径

> tar -zxvf  ma.tar.gz  **-C** /test

## 查看压缩包的内容

> tar -ztvf  ma.tar.gz 

## 打包并压缩车bz2

> tar -jcvf test.tar.bz2

> 解压: tar-jxvf test.bar.bz2

# Linux 开关机和重启

开机: 开机键

关机:shutdown ,halt, init 0 ,poweroff

重启 init6

Shutdown命令详解：

shutdown -h now  ## 立刻关机

shutdown -h +10  ##  10分钟以后关机	

shutdown -h 12:00:00  ##12点整的时候关机
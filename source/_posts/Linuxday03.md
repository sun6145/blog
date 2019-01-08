---
title: Linuxday03
tags:
  - null
  - null
date: 2018-09-15 09:29:17
categories:
top:

---

Linux下的软件安装

- jdk
- mysql
- 进程管理

<!-- more -->

# httpd

> apache http服务器 默认端口80

- service --status-all|grep httpd
- service httpd status: 查看httpd服务
- yum install -y httpd : 下载
- service httpd start/stop http服务的启动与关闭
- /var/www/html : 资源文件的根目录
- /etc/httpd/:配置文件
- 关闭防护墙
- 开启httpd
- 设置ip映射

您现在可以将内容添加到根目录`/ var / www / html /`



# 挂载

> 将设备文件连接到一个已经存在的目录
>
> 挂载源:设备文件
>
> 挂载点: 存在的目录
>
> 临时挂载:mount -t iso9660 -o ro/loop 挂载源 挂载点
>
> 永久挂载:vim /etc/fstab
>
> 卸载: umount 挂载点



> 将一个文件系统的顶层目录挂到另一个文件系统的子目录上，使它们成为一个整体，称为挂载。把该子目录称为挂载点。
>
> 注意：
>
> ​	1、挂载点必须是一个目录。
>
> ​	2、一个分区挂载在一个已存在的目录上，这个目录可以不为空，但挂载后这个目录下以前的内容将不可用。　
>
> ​	3、mount命令没有建立挂载点的功能，因此你应该确保执行mount命令时，挂载点已经存在。（不懂？说白了点就是你要把文件系统挂载到哪，首先要先建上个目录。）
>
> 　　
>
>  　　

> 格式：mount \[-参数\]\[设备名称\][挂载点] 　　
>
> -t<文件系统类型> 指定设备的文件系统类型，常见的有：　　　　
> ​	minix linux最早使用的文件系统　　　　
> ​	ext2 linux目前常用的文件系统　　　　
> ​	msdos MS-DOS的fat，就是fat16 　　　　
> ​	vfat windows98常用的fat32 　　　　
> ​	nfs 网络文件系统　　　　
> ​	**iso9660 CD-ROM光盘标准文件系统**　　　　
> ​	ntfs windows NT 2000的文件系统　　　　
> ​	hpfs OS/2文件系统　　　　
> ​	auto 自动检测文件系统　　　　
>
> -o<选项> 指定挂载文件系统时的选项。有些也可用在/etc/fstab中。常用的有　
> 　　codepage=XXX 代码页　　　　
> ​	iocharset=XXX 字符集　　
>
> ​		 codepage指定文件系统的代码页，简体中文中文代码是936；iocharset指定字符集，简体中文一般用cp936或gb2312。 　　　　
> ​	**ro 以只读方式挂载**　　　　
> ​	**rw 以读写方式挂载**　　　　
> ​	**nouser 使一般用户无法挂载**　　　　
> ​	user 可以让一般用户挂载设备 　　

> mount -t iso9660 /dev/cdrom /mnt/cdrom 　　

## 自动挂载 　　

> 在/etc目录下有个fstab文件，它里面列出了linux开机时自动挂载的文件系统的列表。 

**第一列**：**/dev/cdrom**：挂载源，要挂在的磁盘分区或者存储设备，或者文件系统

**第二列**：**/mnt/cdrom**：挂载点，挂载位置

**第三列**：**iso9660**：文件系统类型，ext4文件系统，swap表示交换分区，……

**第四列**：**defaults**：挂载参数

**第五列**：**0**：表示dump选项，dump工具通过这个选项位置上的数字来决定文件系统是否需要备份。如果是0，dump就会被忽略，事实上大多数的dump设置都是0

**第六列**：**0**：读取文件系统时候的检查顺序，允许的数字是0,1,2，根目录应当获得最高的优先权 1, 其它所有需要被检查的设备设置为2，0表示不检查



# 查看时间和时区

> 1. date -R
> 2. 简写 : date
> 3. **date '+%Y-%m-%d %H:%M:%S'**

## 时区

- 查看时区:**cat /etc/sysconfig/clock**

> CentOS中时区是以文件形式存在，当前正在使用的时区文件位于`/etc/localtime`,其他时区文件则位于`/usr/share/zoneinfo`,其中中国时区使用`/usr/share/zoneinfo/Asia/Shanghai`
>
> PS：如果没有Asia/Shanghai时区文件，请使用tzselect命令去生成时区文件，生成好的时区文件就在/usr/share/zoneinfo目录下

- 修改时区,用时区文件覆盖`/etc/localtime`

  > **cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime**

## 时间

- 我们一般使用”date -s”命令来修改系统时间

```txt
[root@hadoop01 ~]# date -s 12/25/2016
[root@hadoop01 ~]# date -s 19:57:30
[root@hadoop01 ~]# date -s “2016-12-25 19:57:30”
```

- 同步网络时间

  ```
  [root@hadoop01 ~]# ntpdate time.nist.gov
  [root@hadoop01 ~]# ntpdate 202.120.2.101
  上海交通大学网络中心NTP服务器地址：202.120.2.101（ntp.sjtu.edu.cn）
  中国国家授时中心服务器地址：cn.pool.ntp.org
  若以上提供的网络时间服务器不可用，请自行上网寻找可用的网络时间服务器
  ```

- 一劳永逸

  ```
  1、使用命令：crontab -e:编辑当前用户的计划任务
  [root@hadoop01 ~]# crontab -e
  crontab的使用后面会有详细讲解
  
  2、然后往里加入一行内容
  */10 * * * * ntpdate cn.pool.ntp.org
  上面的配置表示，每隔十分钟从202.120.2.101该时间服务器同步一次时间。
  
  保存退出即可
  ```

# 软件安装

## 二进制发布包

> 软件已经针对具体平台编译打包发布，只要解压，修改配置即可

## RPM发布包

软件已经按照RedHat（Redhat Package Manager）的包管理工具规范RPM进行打包发布，需要获取到相应的软件RPM发布包，然后用rpm命令进行安装(如:mysql)

```
1、安装：rpm -ivh 包名
参数：
-i ：安装的意思
-v ：可视化
-h ：显示安装进度
另外在安装一个rpm包时常用的附带参数有：
--force 强制安装，即使覆盖属于其他包的文件也要安装
--nodeps 当要安装的rpm包依赖其他包时，即使其他包没有安装，也要安装这个包

2、升级包：rpm -Uvh filename
-U 升级

3、卸载包rpm -e filename (--nodpes)（这里的filename是通过rpm的查询功能所查询到的）

4、查询一个包是否安装：rpm -q  包名（这里的包名，是不带有平台信息以及后缀名的）

5.查询当前安装的所有rpm包：rpm -qa
查询当前安装的和sql相关的包：rpm -qa | grep 'sql'
查询sqlite安装路径：rpm -ql sqlite
```



## Yum在线安装

**Yum**（**Y**ellow dog **U**pdater, **M**odified）是一个在CentOS和RedHat以及SUSE中的Shell前端软件**包管理器**。比如软件已经以RPM规范打包，但发布在了网络上的一些服务器上，可用yum在线安装服务器上存在的rpm软件，并且会自动解决软件安装过程中的库依赖问题（注：类似于maven），yum提供了查找、安装、删除某一个、一组甚至全部软件包的命令，而且命令简洁而又好记

 

> yum仓库服务器本质上就是一台http服务器，服务器的目录中放置了rpm包，及rpm包的索引信息文件，即可为yum客户端提供rpm文件下载

**安装示例：** **yum install nc**

> yum命令工具使用举例
>
> yum update 升级系统
>
> yum install ~ 安装指定软件包
>
> yum update ~ 升级指定软件包
>
> yum remove ~ 卸载指定软件

## 源码编译安装

软件以源码工程的形式发布，需要获取到源码工程后用相应开发工具进行编译打包部署

**安装示例：资料-Redis的安装**



# 安装JDK

1. linux获取sjdk的发布包,上传sftp

2. scrt:alt+p,默认为当前用户的家目录

3. 解压:/usr/loc   | /opt

4. 配置:

   1. 查询:rpm -qa | grep java

   2. 卸载:rpm -e 程序

      1. error: Failed dependencies:

      ```
      rpm -e java-1.6.0-openjdk-1.6.0.35-1.13.7.1.el6_6.x86_64 --nodeps
      ```

   3. 配置:

      1. vim /etc/profile
      2. export JAVA_HOME=/usr/local/jdk1.8.0_73
         export PATH=$PATH:$JAVA_HOME/bin
      3. 执行配置文件
      4. source /etc/profile

# 安装Mysql

1. 下载上传

2. 查询安装

   > rpm -qa | grep -i mysql

3. 卸载已安装

   > rpm -e 程序名

4. 安装服务器端

   > rpm -ivh MySQL-server-5.6.26-1.linux_glibc2.5.x86_64.rpm 

5. 安装客户端

   > rpm -ivh MySQL-client-5.6.26-1.linux_glibc2.5.x86_64.rpm 

6. 启动服务

   > service mysql start
   >
   > netstat -nltp 查看网络接口

7. 登录

   1. 获取初始密码

      > cat  /root/.mysql_secret

   2. 设置密码

      > set PASSWORD=PASSWORD('root');

   3. 退出登陆验证，看是否改密码成功

   4. 增加远程登录权限

      > GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'root' WITH GRANT OPTION;
      >
      > FLUSH PRIVILEGES;

      > grant 权限 1,权限 2,…权限n on 数据库名称.表名称 to 用户名@用户地址 identified by ‘连接口令’;PS：1,权限 2,…权限 n 代表 select，insert，update，delete，create，drop，index，alter，grant,references，reload，shutdown，process，file 等 14 个权限当权限 1,权限 2,…权限 n 被 all privileges 或者 all 代替，表示赋予用户全部权限。当数据库名称.表名称被*.*代替，表示赋予用户操作服务器上所有数据库所有表的权限。用户地址可以是 localhost，也可以是 ip 地址、机器名字、域名。也可以用’%’地址连接.

   5. mysql配置文件

      > /use/my.cnf复制到/etc/my.cnf

# 配置本地yum 源

> /etc/yum.repos.d/  :配置yum源
>
> 查看可用的yum源: yum repolist

1. 准备一个http服务器

2. 在httpd服务的web目录中放入rpm库，

   ```
   比如，将centos安装光盘中的rpm库放入：
   
   mkdir /var/www/html/centos
   
   cp  -r /mnt/cdrom/ /var/www/html/centos/
   
   注：上面这种方式比较浪费空间，可以不用拷贝，其实只要创建一个软连接即可
   ln -s /mnt/cdrom   /var/www/html/centos
   
   ```

3. 用浏览器访问一下httpd服务，看看是不是能看到这些东西

   地址：http://192.168.123.202/centos

1. 配置yum客户端repo地址文件

> 将本地http服务器加入repo地址

```
   首先，将内置的源全部disable掉
   cd /etc/yum.repos.d/
   rename .repo .repo.bak *
   然后，新建一个repo源：
  
  
  开始-->
  vi  CentOS-Local.repo
   
   [CentOS-Local]
   name=CentOS-Local
   baseurl=http://192.168.123.202/centos/cdrom0
          http://192.168.123.202/centos/cdrom1 
   gpgcheck=0
   enabled=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6
```

1. 接下来，我们检查我们自定义的源是否生效，使用命令：**yum repolist**

2. 再接下来，就可以使用yum像从公网安装软件一样在内网服务器上下载软件进行安装了

   yum install -y 你要的软件



```
在安装完CentOS后一般需要修改yum源，才能够在安装更新rpm包时获得比较理想的速度。国内比较快的有163源、sohu源。这里以163源为例子。

1. cd /etc/yum.repos.d

2. mv CentOS-Base.repo CentOS-Base.repo.backup

3. wget http://mirrors.163.com/.help/CentOS6-Base-163.repo

4. mv CentOS6-Base-163.repo CentOS-Base.repo

5.yum clean all

最后在使用yum 就可以了。
```



# 进程

> 进程是程序的执行过程，相对于程序，进程是动态的，在linux系统中，它与用户权限相关，程序与进程并没有一一对应，一个程序可能对应多个进程。

> 子进程与父进程：一个进程产生另外一个进程，产生的进程称为子进程，生成另外一个进程的进程称为父进程

## 进程状态

> 总体来看主要包括三种，分别是就绪状态、运行状态、等待状态，就绪状态指的是进程除了CPU资源，其他运行资源都已获得，当就就绪状态的进程得到CPU资源时，则进入进程进入运行状态，运行状态的进程在时间片用完后会重新转换成就绪状态，如果在运行时进程遇到IO等阻塞操作，则进程会进入等待状态，等待状态的进程当阻塞操作完成后，进程将进入就绪状态。

## 进程管理

> ps命令:用于报告当前系统的进程状态。

```
ps 命令常用选项包括：
1、a显示所有用户的进程
2、u显示用户名和启动时间
3、x显示所有进程，包括没有控制终端的进程
4、e显示所有进程，包括没有控制终端的进程，较x选项，信息更为简略
5、l显示进程详细信息，按长格式显示

ps -au 显示所有用户进程，并给出用户名和启动时间等详细信息
ps -aux 显示所有用户进程，包括没有控制终端的进程，并给出用户和和启动埋单等详细信息
ps -el 按长格式显示进程详细信息

USER: 进程所有者
PID: 进程号
PPID: 进程的父进程ID
%CPU:  CPU占用率
C: 进程的CPU占用率
%MEM: 内存占用率
VSZ: 表示如果一个程序完全驻留在内存的话需要占用多少内存空间;
RSS: 指明了当前实际占用了多少内存;
TTY: 终端的次要装置号码 (minor device number of tty)
F：进程的标志
S：进程的状态
STAT: 该进程程的状态，有以下值
D: 不可中断的静止
R: 正在执行中
S: 静止状态
T: 暂停执行
Z: 不存在但暂时无法消除
W: 没有足够的记忆体分页可分配
<: 高优先序的进程
N: 低优先序的进程
L: 有记忆体分页分配并锁在记忆体内
PRI：进程的优先权
NI：进程的Nice值
ADDR：进程的地址空间
SZ：进程占用内存的大小
WCHAN：进程当前是否在运行
TTY：进程所属终端
START: 进程开始时间
TIME: 执行的运行时间
COMMAND：所执行的指令
CMD：进程的命令
```

- PID
- %CPU:  CPU占用率
- C: 进程的CPU占用率
- COMMAND：所执行的指令

### kill/pidof/pkill命令

```
1、kill pid  直接杀死进程，但不能保证一定能杀死
2、kill -9 pid  强制杀死进程

3、pidof命令用于查看某个进程的进程号（例如：pidof mysqld）

4、pkill命令可以按照进程名杀死进程。pkill和killall应用方法差不多，也是直接杀死运行中的程序；如果您想杀掉单个进程，请用kill来杀掉
```

### 进程切换

> 前台进程指的是进程在执行时会将命令行阻塞，直到进程执行完毕；
>
> 后台进程指的是进程在执行时不会阻塞当前命令行，而是在系统后台执行

```
1、ctrl + c 终止进程
2、ctrl + z 挂起进程
3、fg命令将进程转换到前台执行
4、bg命令将进程转换到后台执行
5、jobs命令查看任务
```

## top命令

> Top 命令可以定期显示所有正在运行和实际运行并且更新到列表中，
>
> 它显示出 CPU 的使用、内存的使用、交换内存、缓存大小、缓冲区大小、过程控制、用户和更多命令。
>
> 它也会显示内存和 CPU 使用率过高的正在运行的进程。
>
> 按q键退出查看

## pstree命令 

>  将进程间的关系以树结构的形式展示，能清楚看各进程之间的父子关系
>
>  pstree ：以树状形式显示进程

- pstree -p ： 以树状形式显示进程，并且显示进程号

## JPS命令

> JPS命令是JDK提供的一个检查系统是否启动了JVM进程的一个进程。
>
> 不是linux系统自带的。主要任务就是用来检查java进程的。

# 计划任务

> 计划任务在linux的体现主要分为at 和crontab,其中"
>
> at: 通过 at命令安排任务在某一时刻执行一次
>
> crontab: 通过crontab命令,可以在固定的间隔时间执行指定的系统指令 或shell script脚本,时间间隔的单位可以是分钟,小时,日,月,周及以上的任意组合,适合周期性的日志分析和数据备份等工作.

## at



## crontab

1. 安装

   > yum -y install crontabs

2. 服务操作说明

   - service crond start :启动服务
   - service crond stop: 关闭服务
   - service crond restart: 重启服务
   - service crod status :服务的状态

3. 使用

   > - crontab [-u user ] file
   > - crontab [ -u user ]\[-e  -l  -r]
   >
   > -u:指定用户
   >
   > -e:编辑用户的crontab文件。
   >
   > -l:查看用户的crontab 
   >
   > -r:删除用户的crontab;从**/var/spool/cron**目录中删除
   >
   > file：file是命令文件的名字,表示将file做为crontab的任务列表文件并载入crontab。


## 配置说明

```
基本格式 : 
*　　*　　*　　*　　*　　command 
分　 时　 日　 月　 周　 命令 
第1列表示分钟1～59 每分钟用*或者 */1表示; */10表示10分钟
第2列表示小时0～23（0表示0点） 7-9表示：7点到9点之间  多个时间段用逗号隔开7-9,11  */1每小时
第3列表示日期1～31 
第4列表示月份1～12 
第5列标识号星期0～6（0表示星期天） 
第6列要运行的命令
```



## 配置示例

```
*/1 * * * * date >> /root/date.txt
上面的例子表示每分钟执行一次date命令

30 21 * * * /usr/local/etc/rc.d/httpd restart
上面的例子表示每晚的21:30重启apache

45 4 1,10,22 * * /usr/local/etc/rc.d/httpd restart
上面的例子表示每月1、10、22日的4 : 45重启apache

10 1 * * 6,0 /usr/local/etc/rc.d/httpd restart 
上面的例子表示每周六、周日的1 : 10重启apache

0,30 18-23 * * * /usr/local/etc/rc.d/httpd restart
上面的例子表示在每天18 : 00至23 : 00之间每隔30分钟重启apache

0 23 * * 6 /usr/local/etc/rc.d/httpd restart
上面的例子表示每星期六的11 : 00 pm重启apache

* */1 * * * /usr/local/etc/rc.d/httpd restart
上面的例子每一小时重启apache

* 23-7/1 * * * /usr/local/etc/rc.d/httpd restart
上面的例子晚上11点到早上7点之间，每隔一小时重启apache

0 11 4 * mon-wed /usr/local/etc/rc.d/httpd restart
上面的例子每月的4号与每周一到周三的11点重启apache

0 4 1 jan * /usr/local/etc/rc.d/httpd restart 
上面的例子一月一号的4点重启apache
```

# 克隆

1. vim /etc/udev/rules.d/70-persistent-net.rules
   将eth0 删除，然后将eth1换成eth0  之后复制 然后将ATTR{address}=后面的复制

2. 修改网络配置

   > 删除uid和硬件地址
   >
   > 修改ip
   >
   > vim /etc/sysconfig/network-scripts/ifcfg-eth0

3. 修改主机名

   > vim /etc/sysconfig/network

4. 修改主机和ip的映射

   > vim /etc/hosts



# ssh 免密登录

> Secure Shell 安全外壳的协议((一种网络协议),服务器端进行交互的协议
>
> ssh 就是专为远程登录会话和其他网络服务提供安全性的协议,默认端口22



> ssh是有客户端和服务器的软件组成

## 组成

> 服务器端是: sshd的守护程序,在后台运行并相应来自客户端的连接请求
>
> 包含:ssh程序以及scp(远程拷贝),slogin(远程登录),sftp(安全文件传输)等其他应用程序

## ssh认证机制

> 从客户端来看,ssh提供二种认证机制

- 基于口令;知道账户和口令,就可以登录到远程主机

- 基于密钥的安全验证

  1. 生成私钥和公钥
  2. 将公钥放到目标主机的授权池中
  3. 请求访问
  4. 主机通过公钥加密一段数据后响应
  5. 透过私钥解密后登陆目标主机

## 使用

> `ssh  ip/ 域名` : 远程访问某台机器
>
> `scp -r 文件 用户@ip/域名:目录`:远程拷贝
>
> ​	:`scp -r  /root/a.txt root@hdp02:~/`
>
>



## 使用密钥验证

1. ssg-keygen,一路回车,得到公钥和私钥
2. 发送公钥到授权池中` ~/.ssh/`

   > **ssh-copy-id 把公钥复制到远程主机上:ssh-copy-id user@host**
   >
   > $  ssh-copy-id -i .ssh/id_rsa.pub  用户名字@192.168.x.xxx
   >
   > ```
   > ssh-copy-id root@hdp02
   > ```
   >
   > **注意:** ssh-copy-id **将key写到远程机器的 ~/** .ssh/authorized_key.文件中
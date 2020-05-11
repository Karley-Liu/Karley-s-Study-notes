# 配置文件

个人配置文件：vim	.vimrc

全局配置文件：vim	/etc/vimrc

设置文件行号：set nu,set number

设置文件着色与否：syntax on/off

vim模式下如何使用计算器：

1. 进入编辑模式
2. “Ctrl + R"然后输入”=”，此时光标在最后一行，然后输入表达式，回车，显示答案



# 异常退出

删除交换文件恢复正常：rm	-f	.文件名.swp



# 自定义命令

vim	~/.bashrc	映射文件



# 用户管理

添加用户：useradd	用户名

​		-g：指定用户主组

​		-G：指定用户附加组

​		-u：uid，用户id，从500后分发，可自定义

​		-c：注释

修改用户：usermod	选项	用户名

​		-l:修改用户名

例如：usermod	-l	新用户名	旧用户名

删除用户：userdel	选项	用户名

​		-r：删除家目录

注意：创建用户不指定用户组将会自动创建



# 查看进程

ps -ef | grep	服务名

kill	服务名：强制删除进程



# 网络设置

网卡配置文件：/etc/sysconfig/network-scripts/ifcfg-网卡名称(eth0、lo)

eth0文件：

![image-20200201182352552](C:\Users\hp\AppData\Roaming\Typora\typora-user-images\image-20200201182352552.png)

lo文件：

![image-20200201182436373](C:\Users\hp\AppData\Roaming\Typora\typora-user-images\image-20200201182436373.png)

onboot:是否开机启动

bootproto:分配方式，dhcp动态主机分配协议

hwaddr:硬件地址，MAC地址



重启网卡命令：service network start

其他版本无service命令：/etc/init.d network start/restart

如何启动单个网卡：

​		停止某个网卡：ifdown 网卡名

​		开启某个网卡：ifup 网卡名



# 快捷键

-s:软链接

ln -s 原始文件路径 快捷方式路径



# SSH服务

全名：Secure Shell(安全外壳)

分为两种协议：远程连接协议、远程文件传输协议

协议使用默认端口：22

ssh配置文件可修改端口号：vim/etc/ssh/sshconfig

修改端口同时注意防火墙设置

所有服务端口范围：0~65535

查看ssh进程：ps	-ef |grep	ssh

ssh服务启动：service	sshd	start/stop/restart

​						/etc/init.d/sshd



# 远程终端

常见的终端工具：XShell,SecureCRT,Putty

终端之间文件传输也可使用pscp.exe

windows系统不想设置环境目录可以将pscp.exe放在windows系统目录下(C:/windows)

linux与linux系统之间文件传输使用scp命令



# 主机名设置

hostname -f FQDN(全限定域名)

1. 临时设置主机名：hostname 新名
2. 永久设置主机名(要重启)，打开主机名配置文件：/etc/sysconfig/network



# 开机启动项管理服务(chkconfig服务)

列出开机启动项列表：chkconfig --list

查看某服务在0-6模式下状态：chkconfig --list|grep 某服务名字

在5级别模式下启动或关闭某服务：chkconfig --level 5 某服务 on/off

在3和5级别模式下启动或关闭某服务：chkconfig --level 35 某服务 on/off



# 时间校对服务(ntp服务)

上游概念：自己服务器 -> 上一级别服务器 ->上上一级别服务器 -> 根时间服务器

一次性同步时间：ntpdate

ntp ip地址校对查看：http://www.ntp.org.cn/pool.php

永久同步时间：ntpdate 时间校对ip

ntp服务的服务名:ntpd



# 防火墙

防火墙分为软件防火墙和硬件防火墙，除了防火墙之外还有防水墙

在CentOS6中防火墙服务：iptables

在CentOS7中防火墙服务：firewalld

查看防火墙状态：service iptables status

查看防火墙规则命令：iptables -L -n

​		-L : 列出规则

​		-n : 表示将单词以数字形式表达

简单设置防火墙规则：

1. iptables	-add	-I	Input	 -p	tcp	--dport	80	-j	Accept
2. /etc/init.d/iptables	save



# 软件包管理(rpm管理)

查询某个软件安装情况：rpm -qa | grep	关键词

​		-q:查询，query

​		-a:全部，all

写在某个软件：rpm -e 软件名称

存在依赖关系强制卸载：rpm -e 软件包名 --nodeps

安装软件命令：rpm -ivh 软件包完整名称

​	-i:install

​	-v:显示进度

​	-h:以“#”形式显示进度条

## 查看块状设备信息：

lsblk(list block devices)

![image-20200201190544253](C:\Users\hp\AppData\Roaming\Typora\typora-user-images\image-20200201190544253.png)

Name:名称

Size:设备大小

Type:类型

Mountpoint:挂载点



# 计划任务(cron)

## crontab 选项

​	-l:list,指定用户计划任务列表

​	-e:编辑，edit

​	-u:user,指定用户名，不指定表示当前用户	（不重要）

​	-r:remove,删除	（不重要）

## cron书写规则：

1. 以行为单位，一行一个计划
2. 分	时	日	月	周	需要执行命令

​		每天0点0分执行reboot命令：0	0	*	*	*	reboot

### 			取值范围：

​				分：0~59	时：0~23	日：1~31	月：1~12	周：0~6(0表示星期天)

​				*：取值范围中每一个数字	-：做区间表达式，表达1-7，写成：1-7

​				/：表示每多少个，每10分钟一次：*/10

​				，：表示多少取值，在1点，2点，6点执行，在“时”位置写1，2，6

## crontab权限

root可以通过配置设置某些用户不允许设置计划任务

不允许配置文件：/etc/cron.deny	(里面写用户名，一行一个)

可以添加任务权限白名单(cron.allow)



# linux权限管理

linux用户可存取访问身份分为3个类别：owner,group,others，各自有read,write,execute等权限

查看文件属性：ls -l 路径



![image-20200201192613788](C:\Users\hp\AppData\Roaming\Typora\typora-user-images\image-20200201192613788.png)



第一位为文件类型：

​		d：文件夹

​		-：文件

​		l：软链接

​		s：套接字

第二、三、四位：文件所有者权限(r:可读，w:可写，x:可执行)

第五，六，七位：文件所属用户的用户组用户权限

第八，九，十位：不同于文件所属用户用户组用户权限

## 权限设置

#chmod	选项	权限模式	文档名称

​	选项：-R	递归设置，当文档类型为文件夹的时候设置

​	权限模式：该文档需要设置的权限信息

​	文档：文件，文件夹，相对路径，绝对路径

注意：如果要给文档设置权限，root or owner

### 字母形式：

​		给谁设置：

​				u：文件所有者身份	owner

​				g：同组用户	group

​				o：不同组用户	others

​				a：表示all，所有人

​				不指定用户默认给a设置

### 分配方式：

+：表示给具体用户新增权限(依据当前权限)

-：删除用户权限(依据当前权限)

=：将权限设置成具体的值(注重结果)

例如：要求所有者拥有全部权限，同组用户拥有读，写权限，其他用户只读

#chmod	u+x,g+rx,o+r	文件名

当文件拥有者拥有执行权限，文件在终端显为绿色

### 数字形式：

1：可执行	x	--x

2：可写	w	-w-

3：可读，可执行	-wx

4：可读	r	r--

5：可读，可执行	r-x

6：可读，可写	rw-

7：全权限	rwx

例：#chmod	777	a.txt

注意：权限尽量不设置2，3

### 注意：

读权限对于文件夹来说为是否列出目录结构

写权限对于文件夹来说为是否影响用户可以在文件夹下创建/删除/复制/移动



# 属主与属组

属主：文件所有主

属组：用户所属用户组

更改文档所属用户：

#chown	(-R)	username	文件(夹)路径



更改用户所属组：

#chgrp	(-R)	groupname	文件(夹)路径



两个一起改：

#chown	(-R)	username:groupname	文件(夹)路径



# Sudo扩展

sudo配置文件：/etc/sudoers

配置sudo文件：visudo

例如：root	ALL=(ALL)	ALL

root：表示用户名，“%组名”表示用户组

ALL：允许登录主机(地址白名单)

(ALL)：表示以谁的身份执行

ALL：表示当前用户可执行命令，最好以地址形式表示，多个命令使用","分开

使用路径时可以使用文档末行形式：#:!which	命令

普通用户使用管理员权限命令时前要加sudo:sudo 	useradd	kl

普通用户查看权限：sudo -l



# 网络相关命令

ping：检测当前主机与目标主机是否连通

nestat：检测本机各端口的网络连接情况

​		#nestat	-tnlp

​		-t：tcp协议

​		-n：字母换成数字

​		-l：列出状态监听

​		-p：进程相关信息

traceroute：查找当前主机与目标主机之间所有的网关，该命令需要安装(window系统是tracert)

arp：获取MAC地址

​		-a：查看本地缓存mac表

​		-d	主机地址：删除指定缓存记录



# Shell

在shell脚本文件开头写#!/bin/bash，告知系统这个脚本要使用shell解析器

shell文件命名规范：文件名.sh

shell文件必须有执行权限

shell文件中输出字母和数字时要加双引号，纯数字可不使用双引号

执行shell文件时要从当前路径下写，意思是在文件名前加上"./"

## 变量

class_name="karliu"

echo	$class_name

## 反引号

命令行的值赋值给变量使用反引号：

time=`date +"%F %T"`

echo	$time

## 接收用户输入

read	-p	提示信息	变量名

## 运算

shell中运算使用expr

表达式与运算符中一定要有空格

val=`expr 2 + 2`

echo	"值是：$val"



## if	else用法

if	[ $a	==	$b]

then

​	echo	"true"

else

​	echo "false"

fi



## 文件测试运算符

-b	$file	检测文件是否是块设备文件

-c	$file	检测是否字符设备

-d	$file	检测是否是目录

-f	$file	检测是否是普通文件

-r	$file	检测是否可读

-w	$file	检测是否可写

-x	$file	检测是否可执行

-s	$file	检测文件是否为空

-e	$file	检测文件夹目录是否存在



# 软件安装

## 源码安装

ncurses是linux常用终端库

解压压缩包：tar	-zxvf	*.tar.gz

​						tar	-jxvf	*.tar.bz2

-z：通过gzip指令压缩

-x：从压缩包中还原文件

-v：显示解压过程

-f：指定一个文件

-j：支持bzip2解压文件

### 安装步骤：

1. 切换源码文件夹

2. 配置(config/configure/bootstrap)

   可配置项：软件指定安装目录	--prefix	=路径

   ​						需要依赖路径	--with-PACKAGE名	=包所在路径

   ​						不需要依赖包	--without-PACKAGE名

   ​						文件路径，数据存储

3. 编译(make/bootstraped)

4. 安装(make install/bootstraped install)

例如：在当前包目录下

#./configure	--prefix=/usr/local/ncurses

#make

#make install

## 二进制包安装

查看指定文件属于哪个包：#rpm	-qf	文件路径

#rpm	-ivh	rpm包路径

详细见软件包管理

## yum安装

列出当前可安装列表：#yum list

搜索指定关键词：#yum search 名

例如：安装musql

服务端：#yum install mysql-server

初始化：#mysql-se

开启数据库服务：#service mysqld start

输入"mysql-se"后出现两个选项，输出第一个：mysql-secure-installation

数据库存储目录：/var/lib/mysql

配置文件：/etc/my.conf

登录数据库：#mysql	-u用户名	-p



# 数据库备份与还原

## 备份

全量备份：#mysqldump	-uroot	-p明文	-A	>备份文件路径

指定库备份：#mysqldump	-uroot	-p明文	库名	>备份文件路径

多个库备份：#mysqldump -uroot	-p明文	--databases	db1	db2	>备份文件路径

## 还原

use	库名;

mysql命令行：source	备份文件路径

系统命令行：#mysql	-uroot	-p明文	<备份文件路径

## 乱码

window系统使用gbk编码，linux使用utf-8中文编码

mysql命令行：set	names	utf-8



# mysql远程管理工具

项目分为两种架构：B/S架构和C/S架构

B/S架构：B	->	浏览器，S	->	server	(使用典型管理工具PMA,PHPMyAdmin)

C/S架构：C	->	客户端，S	->	server	(navicat,mysqlworkbrach)

处理数据库不允许远程登录问题：

1. use	mysql;

2. 执行：select	host,user	from	user;

3. 将其中一个host值改为”%“，表示可以允许任何地方登录

   刷新权限表或重启数据库：flush	privileges;
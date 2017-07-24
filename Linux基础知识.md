[TOC]
[用户及用户权限](#13-用户及用户权限)
# Linux基础知识
## 1. 一些基本概念
### 1.1 Linux是什么?
&emsp;&emsp;Linux是一套免费使用和自由传播的类Unix操作系统，是一个基于POSIX(一种接口标准)和UNIX的多用户、多任务、支持多线程和多CPU的**操作系统**。  

### 1.2 Linux发行版本
&emsp;&emsp;Linux存在许多不同的发行版本(distributions),如CentOS,Ubuntu,Debian,RHEL(Red Hat),SuSE等等。事实上,我们平常说的Linux严格来说只是指Linux内核,而发行版其实是指使用了不同的软件管理工具(yum,xxxxTODO)以及软件管理模式(主要分为RPM管理方式和DPKG管理方式)的Linux系统,它们仍然是基于Linux内核的。

RPM软件管理方式 | DPKG软件管理方式
---|---
RHEL(Red Hat) | Ubuntu
CentOS | Debian
... | ...

### 1.3 用户/群组及权限
&emsp;&emsp;Linux系统中,各个文件,文件夹等等都是拥有一定权限的,而权限对应的拥有者就是用户和群组了,当然这里还包括一个其他人(Others)的概念。  
&emsp;&emsp;用户这个概念应该都很清楚,可以类比于windows的用户,而群组其实也可以对应windows下的家庭组的概念,剩下的其他人(Others),其实也相当于一个用户,只是说这是一个相对的概念。举个例子,小明(user1)和小王(user2)在同一个学校(Linux系统),但他们俩分别在六年级一班(group1)和六年级二班(group2)。一天,小王的语文书(file)忘了带了,老师就让小王到小明班上去借一本过来,那么这个时候,站在书的角度,书的拥有者(owner)是小明,书上的签名是六年级一班(group),小王既不是一班的,也不是小明,那么他对小明而言就是其他人(Others),而小王需要小明的同意(权限),他才能借到这本书进行使用。
&emsp;&emsp;那么区分用户和群组有什么好处呢?具体来说,区分用户和群组,是便于为不同的文件或目录赋予不同的权限,而同时,文件或目录有相应权限后,才不会被其他用户查看或随意修改、删除等,起一个安全作用。在Linux系统中,root用户拥有最高的权限,可以访问所有用户的所有文件,也可以新增或删除用户,权力直逼小学校长。
&emsp;&emsp;下面是一个权限例子:
```
[root@localhost ~]# ll
total 80
drwxrwxrwx 3 root root  4096 Jun 24 00:25 91yunserverspeeder
-rw-r--r-- 1 root root 63298 Jun 24 00:25 91yunserverspeeder.tar.gz
...
```
具体的权限如下:
- 针对文件:
	- r(read)
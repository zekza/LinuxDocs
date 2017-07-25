[TOC]
[用户及用户权限](#13-用户群组及权限)
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
&emsp;&emsp;Linux系统中,各个文件,目录等等都是拥有一定权限的,而权限对应的拥有者就是用户(owner)和群组(group)了,当然这里还包括一个其他人(others)的概念。  
&emsp;&emsp;用户这个概念大家应该都很清楚,可以类比于windows的用户,而群组其实也可以对应windows下的家庭组的概念,剩下的其他人(others),其实也相当于一个用户,只是说这是一个相对的概念。举个例子,小明(user1)和小王(user2)在同一个学校(Linux系统),但他们俩分别在六年级一班(group1)和六年级二班(group2)。一天,小王的语文书(file)忘了带了,老师就让小王到小明班上去借一本过来,那么这个时候,站在书的角度,书的拥有者(owner)是小明,书上的签名是六年级一班(group),小王既不是小明,也不是一班的,那么他对这本书而言就是其他人(others),同时小王需要小明的同意(权限),他才能借到这本书进行使用。  
&emsp;&emsp;那么区分用户和群组有什么好处呢?简单来说,区分用户和群组,是便于对不同的文件或目录进行管理以及安全的考虑。为文件或目录赋予不同的权限后,用户就只能对某些文件或目录进行操作,从而使其无法影响到系统核心程序,或者某个用户的文件就不会被其他用户查看或随意修改、删除等,这就起到了安全作用。而在Linux系统中,root用户拥有最高权限,可以访问所有用户的所有文件(不论权限),也可以新增或删除用户,权力直逼小学校长,所以这个账号非常重要。  
&emsp;&emsp;下面看一个例子:
```
[root@localhost ~# ll
total 80
drwxrwxrwx 3 root root  4096 Jun 24 00:25 91yunserverspeeder
-rw-r--r-- 1 root root 63298 Jun 24 00:25 91yunserverspeeder.tar.gz
...
```

&emsp;&emsp;先解释下文件权限的意义,这里权限分两类,一类是针对文件的,一类是针对目录的,每类都有rwx三种权限,如下:  

- 针对文件:
	- r(read):该文件可被读取。
	- w(write):该文件可被编辑。
	- x(execute):该文件可被执行(针对sh等类型的s文件,txt这种文本文件就无所谓了)。
	- -:无权限。
- 针对目录:
	- r(read):该目录可被查询(ls命令)。
	- w(write):目录本身可写,可编辑,可删除,对子文件/子目录而言可新增、更名、移动、删除(删除这个要注意,如果当前文件/目录对于当前用户而言即便是只读甚至不可读的,但是对上级目录有w权限的当前用户仍然可以删除这个子文件/子目录)。
	- x(execute):可以进入该目录(cd命令)。
	- -:无权限。  

然后看`drwxrwxrwx 3 root root`这里,对应关系如下:  

d | rwx | rwx | rwx | 3 | root | root
---|---|---|---|---|---|---
文件类型 | owner权限 | group权限 | others权限 | 文件连结数 | owner | group
  
其中文件类型列,值为`"-"`时指普通文件,`"d"`指目录。  
再之后的第一个`root`,就是文件的当前拥有者(owner),第二个root,就是文件所属群组(group),中间`3`指连结数,暂且不管。

### 1.4 Linux文件结构简述
&emsp;&emsp;Linux和windows管理文件的方式不同,Linux下,是以树形结构来展示的,如下:  
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;![树形结构](https://github.com/JustNeedOneMoreStep/LinuxDocs/blob/master/images/dirtree.gif?raw=true)  
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;树形结构示意图  

&emsp;&emsp;Linux没有C盘D盘这种说法,而是以`"/"`为根目录,向下扩展。举个例子,对于`arod`目录,那么它的完整路径就是`/home/arod`。

## 2.Linux远程工具
&emsp;&emsp;Linux有很多远程工具,例如Putty、XShell、SecureCRT、SSH Secure Shell Slient、openssh等等,可以自行选择,这里只介绍XShell。  

### 2.1 XShell下载
- [官方下载地址](https://www.netsarang.com/download/down_xsh.html)
- [绿色版下载地址](https://www.portablesoft.org/xshell/)  
![绿色版下载说明](https://github.com/JustNeedOneMoreStep/LinuxDocs/blob/master/images/xshelldwurl.png?raw=true)  

### 2.2 XShell使用
- [配置步骤参考链接](https://github.com/JustNeedOneMoreStep/LinuxDocs/blob/master/XShell%E4%BD%BF%E7%94%A8.md)  

&emsp;&emsp;进入到操作主页面:  

&emsp;&emsp;&emsp;&emsp;![使用步骤6](https://github.com/JustNeedOneMoreStep/LinuxDocs/blob/master/images/xs6.png?raw=true)  

这里,注意下登录信息下面那一行`[root@study ~]#`:  

root | study | ~ | #
---|---|---|---
当前用户 | 主机名称 | 当前所在目录 | 用户类型  

其中,主机类型不用去关注,后面的用户类型,只有`root`为`#`,普通用户则为`$`。  

## 3 常用命令介绍

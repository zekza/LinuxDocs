[TOC]

[用户及用户权限](#13-用户群组及权限)
# Linux基础知识
## 1. 一些基本概念
### 1.1 Linux是什么?
Linux是一套免费使用和自由传播的类Unix操作系统，是一个基于POSIX(一种接口标准)和UNIX的多用户、多任务、支持多线程和多CPU的**操作系统**。  

### 1.2 Linux发行版本
Linux存在许多不同的发行版本(distributions),如CentOS,Ubuntu,Debian,RHEL(Red Hat),SuSE等等。事实上,我们平常说的Linux严格来说只是指Linux内核,而发行版其实是指使用了不同的软件管理工具(yum,xxxxTODO)以及软件管理模式(主要分为RPM管理方式和DPKG管理方式)的Linux系统,它们仍然是基于Linux内核的。

RPM软件管理方式 | DPKG软件管理方式
---|---
RHEL(Red Hat) | Ubuntu
CentOS | Debian
... | ...

### 1.3 用户/群组及权限
Linux系统中,各个文件,目录等等都是拥有一定权限的,而权限对应的拥有者就是用户(owner)和群组(group)了,当然这里还包括一个其他人(others)的概念。  

用户这个概念大家应该都很清楚,可以类比于qq上的各用户,Linux下每个用户都拥有各自的私有文件;而群组类似于qq群,通过把一个或多个用户邀请到某一个群里面来,群里的各个人员就可以访问群的共享资源,不论是哪一个人上传的。Linux系统中同一群组的用户,可以根据所属于该群组的文件或目录对应的群组权限进行操作。剩下的其他人(others),其实也相当于一个用户,只是说这是一个相对的概念。  

举个例子:小明(user1)、小红(user2)和小王(user3)在同一个学校(Linux系统),但小明和小红在六年级一班(group1),而小王在六年级二班(group2)。一天,小王的语文书(file)忘了带了,老师就让小王到小明班上去借一本过来,那么这个时候,站在书的角度,书的所属用户,也就是它的拥有者(owner)是小明,书上的签名是六年级一班(group1),小王既不是小明,也不是一班的,那么他对这本书而言就是其他人(others),他需要借到这本书才能使用,不凑巧的是,小明班也上语文课,同时小明的同桌小红也没有带语文书,但这个时候她就不需要单独借小明的书(同属于六年级一班),可以直接和小明一起看,于是小明没办法借给小王,小王得不到小明的许可(权限),于是就没有办法拿到语文书。  

![](https://github.com/JustNeedOneMoreStep/LinuxDocs/blob/master/images/user_group.png?raw=true)  

那么区分用户和群组有什么好处呢?简单来说,区分用户和群组,是便于对不同的文件或目录进行管理以及安全的考虑。为文件或目录赋予不同的权限后,用户就只能对某些文件或目录进行操作,从而使其无法影响到系统核心程序,或者某个用户的文件就不会被其他用户查看或随意修改、删除等,这就起到了安全作用。而在Linux系统中,root用户拥有最高权限,可以访问所有用户的所有文件(不论权限),也可以新增或删除用户,权力直逼小学校长,所以这个账号非常重要。  
下面看一个例子:
```
[root@localhost ~]# ll
total 80
drwxrwxrwx 3 root root  4096 Jun 24 00:25 91yunserverspeeder
-rw-r--r-- 1 root root 63298 Jun 24 00:25 91yunserverspeeder.tar.gz
...
```

先解释下文件权限的意义,这里权限分两类,一类是针对文件的,一类是针对目录的,每类都有rwx三种权限,如下:  

- 针对文件:
	- r(read):该文件可被读取。
	- w(write):该文件可被编辑。
	- x(execute):该文件可被执行(针对sh等类型的文件,txt这种文本文件就无所谓了)。
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
Linux和windows管理文件的方式不同,Linux下,是以树形结构来展示的,如下:  
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;![树形结构](https://github.com/JustNeedOneMoreStep/LinuxDocs/blob/master/images/dirtree.gif?raw=true)  
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;树形结构示意图  

Linux没有C盘D盘这种说法,而是以`"/"`为根目录,向下扩展。举个例子,对于`arod`目录,那么它的完整路径就是`/home/arod`。

## 2.Linux远程工具
Linux有很多远程工具,例如Putty、XShell、SecureCRT、SSH Secure Shell Slient、openssh等等,可以自行选择,这里只介绍XShell。  

### 2.1 XShell下载
- [官方下载地址](https://www.netsarang.com/download/down_xsh.html)
- [绿色版下载地址](https://www.portablesoft.org/xshell/)  
![绿色版下载说明](https://github.com/JustNeedOneMoreStep/LinuxDocs/blob/master/images/xshelldwurl.png?raw=true)  

### 2.2 XShell使用
- [配置参考步骤](https://github.com/JustNeedOneMoreStep/LinuxDocs/blob/master/XShell%E4%BD%BF%E7%94%A8.md)  

最后进入到操作主界面:  

![使用步骤6](https://github.com/JustNeedOneMoreStep/LinuxDocs/blob/master/images/xs6.png?raw=true)  

这里,注意下登录信息下面那一行`[root@study ~]#`:  

root | study | ~ | #
---|---|---|---
当前用户 | 主机名称 | 当前所在目录 | 用户类型  

其中,主机名称不用去关注,后面的用户类型,只有`root`为`#`,普通用户则为`$`。  

## 3 Linux常用命令
### 3.1 命令简述
Linux系统中,使用命令的格式如下:

```
command [-options] parameter1 parameter2 ...
命令名称  命令选项   参数1      参数2      ...
```
这其中,`options`是可选的,但使用时需要注意前面有时为`-`,例如`-h`,也有时使用选项的全称,如`--help`。参数之间使用**空格**来区分。

> Tip:假如一个命令你不知道它有哪些参数,可以尝试先用`command -h`或`comman --help`这两种方式来看它的帮助说明。  

其次,Linux系统命令对大小写敏感,例如`cd`命令,输入`CD`就会报`command not found`这样的错误。

### 3.3 列出文件--ls
平常,我们登录进入XShell后,经常会想查看下当前目录下有哪些文档,就可以使用`ls`命令:
```
[root@localhost home]# ls
admin                             libmcrypt-2.5.8-9.el6.x86_64.rpm             serverspeederbin.txt
epel-release-latest-6.noarch.rpm  mysql57-community-release-el6-11.noarch.rpm  serverspeeder.sh
```

ls命令有很多参数,可以使用`ls --help`来查看,不过我们常用的只有几个:
```
-a, --all                  显示所有文件(包括隐藏文件)
-h, --human-readable       和-l一起用时,展示具有可读性的文件大小,比如1K 234M 2G
-l                         显示详细信息
```
使用示例:
```
[root@localhost home]# ls -a
.   admin                             libmcrypt-2.5.8-9.el6.x86_64.rpm             serverspeederbin.txt  .test
..  epel-release-latest-6.noarch.rpm  mysql57-community-release-el6-11.noarch.rpm  serverspeeder.sh

[root@localhost home]# ls -l
total 248
drwx------ 7 admin admin  4096 Jul 25 08:19 admin
-rw-r--r-- 1 root  root  14540 Nov  5  2012 epel-release-latest-6.noarch.rpm
-rw-r--r-- 1 root  root  97932 Jul  9  2010 libmcrypt-2.5.8-9.el6.x86_64.rpm
-rw-r--r-- 1 root  root  25664 Apr 27 06:45 mysql57-community-release-el6-11.noarch.rpm
-rw-r--r-- 1 root  root  96179 Jun 24 00:23 serverspeederbin.txt
-rw-r--r-- 1 root  root   6156 Jun 24 00:23 serverspeeder.sh

[root@localhost home]# ls -al
total 256
drwxr-xr-x.  3 root  root   4096 Jul 25 08:27 .
dr-xr-xr-x. 24 root  root   4096 Jun 24 12:45 ..
drwx------   7 admin admin  4096 Jul 25 08:19 admin
-rw-r--r--   1 root  root  14540 Nov  5  2012 epel-release-latest-6.noarch.rpm
-rw-r--r--   1 root  root  97932 Jul  9  2010 libmcrypt-2.5.8-9.el6.x86_64.rpm
-rw-r--r--   1 root  root  25664 Apr 27 06:45 mysql57-community-release-el6-11.noarch.rpm
-rw-r--r--   1 root  root  96179 Jun 24 00:23 serverspeederbin.txt
-rw-r--r--   1 root  root   6156 Jun 24 00:23 serverspeeder.sh
-rw-r--r--   1 root  root      0 Jul 25 08:27 .test   #这是一个隐藏文件

[root@localhost home]# ls -alh
total 256K
drwxr-xr-x.  3 root  root  4.0K Jul 25 08:27 .
dr-xr-xr-x. 24 root  root  4.0K Jun 24 12:45 ..
drwx------   7 admin admin 4.0K Jul 25 08:19 admin
-rw-r--r--   1 root  root   15K Nov  5  2012 epel-release-latest-6.noarch.rpm
-rw-r--r--   1 root  root   96K Jul  9  2010 libmcrypt-2.5.8-9.el6.x86_64.rpm
-rw-r--r--   1 root  root   26K Apr 27 06:45 mysql57-community-release-el6-11.noarch.rpm
-rw-r--r--   1 root  root   94K Jun 24 00:23 serverspeederbin.txt
-rw-r--r--   1 root  root  6.1K Jun 24 00:23 serverspeeder.sh
-rw-r--r--   1 root  root     0 Jul 25 08:27 .test    #这是一个隐藏文件


```

这里要提一句的是,Linux中隐藏文件和windows不一样,所谓的隐藏文件,是指文件以`.`开头命名的文件,而并不是文件的属性,上面的`.test`就是一个隐藏文件。  

### 3.2 显示当前路径--pwd
我们登录Linux后,如果想要看自己在哪个目录下,那么就可以使用`pwd`这个命令了:
```
[root@localhost home]# pwd
/home
```
就是这么简单。
### 3.3 切换目录--cd
这个时候要做正事了,那么往往需要切换到我们要操作的那个目录去,这就需要使用`cd`这个命令,虽然`cd`命令也有参数,然而我们基本用不上:
```
[root@localhost home]# cd admin     #从上面知道admin是个目录
[root@localhost admin]# pwd
/home/admin

[root@localhost home]# cd /home/admin
[root@localhost admin]# pwd
/home/admin

```
上面两种方式效果是一样的,只不过下面使用的是绝对路径。  
那么这个时候问题来了,假如我们想要返回上一级,但是又忘了上级目录的名字,又不想用pwd,怎么办呢?很简单,使用`cd ..`就行了,这个`..`代表的就是上级目录,这和java中是一样的,代表相对路径:
```
[root@localhost admin]# cd ..
[root@localhost home]# pwd
/home
```

### 3.4 查看文件--cat,tail


### 3.5 编辑文件--nano,vi

### 3.6 创建目录--mkdir

### 3.7 删除文件/目录--rm

### 3.8 复制文件/目录--cp

### 3.9 移动文件/目录--mv

### 3.10 修改权限--chmod

### 3.11 修改文件/目录的所属用户/群组--chown,chgrp

### 3.12 查询进程--ps

### 3.13 结束进程--kill

### 3.14 查看系统状态--top

### 3.15 查看磁盘空间使用情况--df

### 3.16 远程登录--ssh

### 3.17上传/下载--rz,sz

## 4.windows下检测Linux主机的网络及端口--ping/telnet


## 5.tomcat的安装

## 6.网址推荐
[鳥哥的 Linux 私房菜](http://linux.vbird.org)
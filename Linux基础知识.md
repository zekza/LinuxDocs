[TOC]

[用户及用户权限](#13-用户群组及权限)
[tomcat的安装](#5tomcat的安装)
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

举个例子:小明(user1)、小红(user2)和小王(user3)在同一所小学(Linux系统)上学,但小明和小红在六年级一班(group1),而小王在六年级二班(group2)。一天,小王的语文书(file)忘了带了,老师就让小王到小明班上去借一本过来,那么这个时候,站在书的角度,书的所属用户,也就是它的拥有者(owner)是小明,书上的签名是六年级一班(group1),小王既不是小明,也不是一班的,那么他对这本书而言就是其他人(others),他需要借到这本书才能使用,不凑巧的是,小明班也上语文课,同时小明的同桌小红也没有带语文书,但这个时候她就不需要单独借小明的书(同属于六年级一班),可以直接和小明一起看,于是小明没办法借给小王,小王得不到小明的许可(权限),于是就没有办法拿到语文书。  

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

### 3.4 查看文本内容--cat,tail
查看文件内容的命令其实还有很多,比如tac,nl,more,less等等,但就我们平常使用而言这两个就足够了,再不够下一节点的那两个也可以临时用作文本查看。  

#### 3.4.1 cat
同样的,cat命令也可以使用`cat --help`查看帮助说明,不过平时使用的话可能最多会使用到`-n`:
```
-n, --number             对输出的所有行编号
```
使用示例:
```
[root@study ~]# cat -n aaa.txt 
     1	用法：cat [选项]... [文件]...
     2	将[文件]或标准输入组合输出到标准输出。
     3	
     4	  -A, --show-all           等于-vET
     5	  -b, --number-nonblank    对非空输出行编号
     6	  -e                       等于-vE
     7	  -E, --show-ends          在每行结束处显示"$"
     8	  -n, --number             对输出的所有行编号
     9	  -s, --squeeze-blank      不输出多行空行
    10	  -t                       与-vT 等价
    11	  -T, --show-tabs          将跳格字符显示为^I
    12	  -u                       (被忽略)
    13	  -v, --show-nonprinting   使用^ 和M- 引用，除了LFD和 TAB 之外
    14	      --help		显示此帮助信息并退出
    15	      --version		显示版本信息并退出
```

#### 3.4.2 tail
tail翻译成中文是"尾巴"的意思,所以从名字就可以看出来它的作用,这个命令表示从文档结尾处开始查看,其选项如下:
```
-n, --lines=K            n是具体的数字,显示最后n行
-f, --follow[={name|descriptor}]            持续检测文件的输出文本(很适合跟踪实时日志)
```
使用示例:
```
[root@study ~]# tail -10 aaa.txt 
  -e                       等于-vE
  -E, --show-ends          在每行结束处显示"$"
  -n, --number             对输出的所有行编号
  -s, --squeeze-blank      不输出多行空行
  -t                       与-vT 等价
  -T, --show-tabs          将跳格字符显示为^I
  -u                       (被忽略)
  -v, --show-nonprinting   使用^ 和M- 引用，除了LFD和 TAB 之外
      --help		显示此帮助信息并退出
      --version		显示版本信息并退出

[root@study ~]# tail -f aaa.txt 
  -E, --show-ends          在每行结束处显示"$"
  -n, --number             对输出的所有行编号
  -s, --squeeze-blank      不输出多行空行
  -t                       与-vT 等价
  -T, --show-tabs          将跳格字符显示为^I
  -u                       (被忽略)
  -v, --show-nonprinting   使用^ 和M- 引用，除了LFD和 TAB 之外
      --help		显示此帮助信息并退出
      --version		显示版本信息并退出
新的输出  2017年 06月 18日 星期日 21:57:02 CST
新的输出  2017年 06月 18日 星期日 21:58:01 CST
```
由于`tail -f`的持续输出,会导致我们的界面一直在文本的输出状态,如果这时候要退出,那么按`Ctrl+C`即可。

### 3.5 编辑文本--nano,vi
文本编辑是经常会用到的功能,这两个命令本身还是有些复杂,不过我们通常只是简单地使用,毕竟在命令窗口下编辑文本还是有些麻烦,所以可以将文件下载下来进行修改,然后再上传上去覆盖。但是如果只是简单的增减内容,倒是可以直接就用它们俩了。
#### 3.5.1 nano

虽然我们使用`nano -h`可以取得说明信息,但是基本上用不上那些选项..倒是一些快捷键需要知道:  

![](https://github.com/JustNeedOneMoreStep/LinuxDocs/blob/master/images/nano.png?raw=true)  

如图,当前已经进入到`aaa.txt`这个文件里面了。可以看到,下方很多`^G`这样的功能,这里`^`代表的是`Ctrl`键,`^G`就是指`Ctrl+G`。  
之后我们可以使用键盘上的`↑↓←→`移动光标,移动到指定位置,输入我们需要的信息,最后既可以使用`^O`的方式先保存,然后`^X`退出,也可以直接`^X`退出,系统提示保存:

```
储存更动过的缓冲区吗(回答 "No" 会撤销修改)？                                                                      
 Y 是
 N 否           ^C 取消
```
输入Y,然后提示:
```
要写入的文件名: aaa.txt                                                                                           
^G 求助                     M-D DOS 格式                M-A 附加                    M-B 备份文件
^C 取消                     M-M Mac 格式                M-P 前引
```
下方的按键都不需要在意,在这里如果想取消保存并返回编辑就按`^C`,否则就检查文件名,如果要覆盖直接按`[Enter]`键确认即可,重命名会有其他选项,自己操作时看文字就明白,就不再细说了。

#### 3.5.2 vi

vi命令是Linux系统默认会调用的文本编辑器,它的进阶版有vim,相比之下增加了一些功能,不过基础使用是一致的。vi比nano强大多了,并分为三种工作模式,分别是命令模式、文本输入模式、和末行模式。  

- **命令模式**:vi打开文件后默认所处的模式,同时如果在其他模式中,按下`[Esc]`也可以返回到这种模式。  
常用功能:
```
ndd：删除当前光标所在行及其后n-1行
/pattern：从光标开始处向后搜索pattern(pattern是要搜索的内容)
?pattern：从光标开始处向前搜索pattern
n：在同一方向重复上一次搜索命令(使用/pattern就是查找下一个)
N：在反方向上重复上一次搜索命令
```
第一个需要说明下,`n`是我们按下的一个数字,然后连按两下`d`键就可以删除内容了,如果只按两下`d`则只删除当前光标所在行。  
`/pattern`和`?pattern`则如下,控制台先输入`vi linux.txt`:
```
linux 操作系统的诞生
  ...
  2001年1月，Linux 2.4发布，它进一步地提升了SMP系统的扩展性，同时它也集成了很多用于支持桌面系统的特性：USB，PC卡（PCMCIA）的支持，内置的即插即用，等等功能。
  2003年12月，Linux 2.6版内核发布，相对于2.4版内核2.6在对系统的支持都有很大的变化。
  2004年的第1月，SuSE嫁到了Novell，SCO继续顶着骂名四处强行“化缘”， Asianux， MandrakeSoft也在五年中首次宣布季度赢利>。3月，SGI宣布成功实现了Linux操作系统支持256个Itanium 2处理器。
~                                                                                                                 
"linux.txt" 18L, 3041C
```
然后按下"/"或"?",在末行位置就会像下面这样显示:
```
linux 操作系统的诞生
  ...
  2001年1月，Linux 2.4发布，它进一步地提升了SMP系统的扩展性，同时它也集成了很多用于支持桌面系统的特性：USB，PC卡（PCMCIA）的支持，内置的即插即用，等等功能。
  2003年12月，Linux 2.6版内核发布，相对于2.4版内核2.6在对系统的支持都有很大的变化。
  2004年的第1月，SuSE嫁到了Novell，SCO继续顶着骂名四处强行“化缘”， Asianux， MandrakeSoft也在五年中首次宣布季度赢利>。3月，SGI宣布成功实现了Linux操作系统支持256个Itanium 2处理器。
~     
				   #原来的"linux.txt" 18L, 3041C没有了
/                  #按下"/"或"?"后,该行会出现光标等待输入
				   #然后输入要搜索的关键字后敲[Enter]即可
```

- **文本输入模式**:vi进入文件后,按下`a`,`i`,`o`等就可以进入文本输入模式,常用的按键如下:
```
i ：在光标前开始编辑
I ：在当前行首开始编辑
a ：光标后开始编辑
A ：在当前行尾开始编辑
o ：在当前行之下新开一行开始编辑
O ：在当前行之上新开一行开始编辑
```

- **末行模式**:使用vi进入文件后,按下`:`即可输入末行模式。常用的末行模式命令如下:
```
:w ：保存当前文件
:x ：保存当前文件并退出vi,等同于:wq
:q ：退出vi
:q! ：不保存文件并强制退出vi
```  

如果想要了解更多vi的用法,可以参考[Linux文件编辑命令详细整理](http://blog.csdn.net/u013142781/article/details/50735470)
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
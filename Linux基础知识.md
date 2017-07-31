# Linux基础知识
## 1. 一些基本概念
### 1.1 Linux是什么?
Linux是一套免费使用和自由传播的类Unix操作系统，是一个基于POSIX(一种接口标准)和UNIX的多用户、多任务、支持多线程和多CPU的**操作系统**。  

### 1.2 Linux发行版本
Linux存在许多不同的发行版本(distributions)，如CentOS，Ubuntu，Debian，RHEL(Red Hat)，SuSE等等。事实上，我们平常说的Linux严格来说只是指Linux内核，而发行版其实是指使用了不同的软件管理工具(yum，xxxxTODO)以及软件管理模式(主要分为RPM管理方式和DPKG管理方式)的Linux系统，它们仍然是基于Linux内核的。

RPM软件管理方式 | DPKG软件管理方式
---|---
RHEL(Red Hat) | Ubuntu
CentOS | Debian
... | ...

### 1.3 用户/群组及权限
Linux系统中，各个文件，目录等等都是拥有一定权限的，而权限对应的拥有者就是用户(owner)和群组(group)了，当然这里还包括一个其他人(others)的概念。  

用户这个概念大家应该都很清楚，可以类比于qq上的各用户，Linux下每个用户都拥有各自的私有文件;而群组类似于qq群，通过把一个或多个用户邀请到某一个群里面来，群里的各个人员就可以访问群的共享资源，不论是哪一个人上传的。Linux系统中同一群组的用户，可以根据所属于该群组的文件或目录对应的群组权限进行操作。剩下的其他人(others)，其实也相当于一个用户，只是说这是一个相对的概念。  

> 举个例子：小明(user1)、小红(user2)和小王(user3)在同一所小学(Linux系统)上学，但小明和小红在六年级一班(group1)，而小王在六年级二班(group2)。一天，小王的语文书(file)忘了带了，老师就让小王到小明班上去借一本过来，那么这个时候，站在书的角度，书的所属用户，也就是它的拥有者(owner)是小明，书上的签名是六年级一班(group1)，小王既不是小明，也不是一班的，那么他对这本书而言就是其他人(others)，他需要借到这本书才能使用，不凑巧的是，小明班也上语文课，同时小明的同桌小红也没有带语文书，但这个时候她就不需要单独借小明的书(同属于六年级一班)，可以直接和小明一起看，于是小明没办法借给小王，小王得不到小明的许可(权限)，于是就没有办法拿到语文书。  
![](https://github.com/JustNeedOneMoreStep/LinuxDocs/blob/master/images/user_group.png?raw=true)  

那么区分用户和群组有什么好处呢?简单来说，区分用户和群组，是便于对不同的文件或目录进行管理以及安全的考虑。为文件或目录赋予不同的权限后，用户就只能对某些文件或目录进行操作，从而使其无法影响到系统核心程序，或者某个用户的文件就不会被其他用户查看或随意修改、删除等，这就起到了安全作用。而在Linux系统中，root用户拥有最高权限，可以访问所有用户的所有文件(不论权限)，也可以新增或删除用户，权力直逼小学校长，所以这个账号非常重要。  
下面看一个例子：
```
[root@localhost ~]# ll
total 80
drwxrwxrwx 3 root root  4096 Jun 24 00:25 91yunserverspeeder
-rw-r--r-- 1 root root 63298 Jun 24 00:25 91yunserverspeeder.tar.gz
...
```

先解释下文件权限的意义，这里权限分两类，一类是针对文件的，一类是针对目录的，每类都有rwx三种权限，如下：  

- 针对文件：
	- r(read)：该文件可被读取。
	- w(write)：该文件可被编辑(简称可写)。
	- x(execute)：该文件可被执行(针对sh等类型的文件，txt这种文本文件就无所谓了)。
	- -：无权限。
- 针对目录：
	- r(read)：该目录可被查询(ls命令)。
	- w(write)：目录本身可写，可编辑，可删除，对子文件/子目录而言可新增、更名、移动、删除(删除这个要注意，如果当前文件/目录对于当前用户而言即便是只读甚至不可读的，但是对上级目录有w权限的当前用户仍然可以删除这个子文件/子目录)。
	- x(execute)：可以进入该目录(cd命令)。
	- -：无权限。  

然后看`drwxrwxrwx 3 root root`这里，对应关系如下：  

d | rwx | rwx | rwx | 3 | root | root
---|---|---|---|---|---|---
文件类型 | owner权限 | group权限 | others权限 | 文件连结数 | owner | group
  
其中文件类型列，值为`"-"`时指普通文件，`"d"`指目录。  
再之后的第一个`root`，就是文件的当前拥有者(owner)，第二个root，就是文件所属群组(group)，中间`3`指连结数，也就是有几个快捷方式指向它，暂且不管。  

同时权限`rwx-`还分别对应一个数字：  

- r：4
- w：2
- x：1
- -：0  

使用数值有什么好处呢?Linux系统中，权限值是可以用来相加的，比如要给一个文件的某类用户(如others)授予rw权限而不授予x权限，也就是`rw-`这样的，那么对应的数字就可以简化为`4+2+0=6`，也就是直接使用`6`就可以代替`rw-`了。  
> 例如，刚开始使用Linux的时候，可能会比较喜欢用`chmod 777 filename`命令来授权(当然777是授予所有权限了，一般不建议这么用)，这里`7=4+2+1`等同于`rwx`权限，3个`7`则就是分别对owner，group和others进行授权，其效果和`chmod u=rwx，g=rwx，o=rwx filename`相同，那么，当你授权时你会选择哪种方式呢?显而易见嘛。当然`chmod`这个授权的命令在后面会进行介绍。  

### 1.4 Linux文件结构简述
Linux和windows管理文件的方式不同，Linux下，是以树形结构来展示的，如下：  
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;![树形结构](https://github.com/JustNeedOneMoreStep/LinuxDocs/blob/master/images/dirtree.gif?raw=true)  
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;树形结构示意图  

Linux没有C盘D盘这种说法，而是以`"/"`为根目录，向下扩展。举个例子，对于`arod`目录，那么它的完整路径就是`/home/arod`。

## 2.Linux远程登录工具
Linux有很多远程登录工具，例如Putty、XShell、SecureCRT、SSH Secure Shell Slient、openssh等等，可以自行选择，这里只介绍XShell。  

### 2.1 XShell下载
- [官方下载地址](https://www.netsarang.com/download/down_xsh.html)
- [绿色版下载地址](https://www.portablesoft.org/xshell/)  
![绿色版下载说明](https://github.com/JustNeedOneMoreStep/LinuxDocs/blob/master/images/xshelldwurl.png?raw=true)  

### 2.2 XShell使用
- [配置参考步骤](https://github.com/JustNeedOneMoreStep/LinuxDocs/blob/master/XShell%E4%BD%BF%E7%94%A8.md)  

最后进入到操作主界面：  

![使用步骤6](https://github.com/JustNeedOneMoreStep/LinuxDocs/blob/master/images/xs6.png?raw=true)  

这里，注意下登录信息下面那一行`[root@study ~]#`：  

root | study | ~ | #
---|---|---|---
当前用户 | 主机名称 | 当前所在目录 | 用户类型  

其中，主机名称不用去关注，后面的用户类型，只有`root`为`#`，普通用户则为`$`。  

## 3 Linux常用命令
### 3.1 命令简述
Linux系统中，使用命令的格式如下：

```
command [-options] parameter1 parameter2 ...
命令名称  命令选项   参数1      参数2      ...
```
这其中，`options`是可选的，但使用时需要注意前面有时为`-`，例如`-h`，也有时使用选项的全称，如`--help`。参数之间使用**空格**来区分。

> Tip：假如一个命令你不知道它有哪些参数，可以尝试先用`command -h`或`command --help`这两种方式来看它的帮助说明。  

其次，Linux系统命令对大小写敏感，例如`cd`命令，输入`CD`就会报`command not found`这样的错误。

### 3.3 列出文件--ls
平常，我们登录进入XShell后，经常会想查看下当前目录下有哪些文档，就可以使用`ls`命令：
```
[root@localhost home]# ls
admin                             libmcrypt-2.5.8-9.el6.x86_64.rpm             serverspeederbin.txt
epel-release-latest-6.noarch.rpm  mysql57-community-release-el6-11.noarch.rpm  serverspeeder.sh
```

ls命令有很多参数，可以使用`ls --help`来查看，不过我们常用的只有几个：
```
-a, --all                  显示所有文件(包括隐藏文件)
-h, --human-readable       和-l一起用时，展示具有可读性的文件大小，比如1K 234M 2G
-l                         显示详细信息
```
使用示例：
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

这里要提一句的是，Linux中隐藏文件和windows不一样，所谓的隐藏文件，是指文件以`.`开头命名的文件，而并不是文件的属性，上面的`.test`就是一个隐藏文件。  

### 3.2 显示当前路径--pwd
我们登录Linux后，如果想要看自己在哪个目录下，那么就可以使用`pwd`这个命令了：
```
[root@localhost home]# pwd
/home
```
就是这么简单。
### 3.3 切换目录--cd
这个时候要做正事了，那么往往需要切换到我们要操作的那个目录去，这就需要使用`cd`这个命令，虽然`cd`命令也有参数，然而我们基本用不上：
```
[root@localhost home]# cd admin     #从上面知道admin是个目录
[root@localhost admin]# pwd
/home/admin

[root@localhost home]# cd /home/admin
[root@localhost admin]# pwd
/home/admin

```
上面两种方式效果是一样的，只不过下面使用的是绝对路径。  
那么这个时候问题来了，假如我们想要返回上一级，但是又忘了上级目录的名字，又不想用pwd，怎么办呢?很简单，使用`cd ..`就行了，这个`..`代表的就是上级目录，这和java中是一样的，代表相对路径：
```
[root@localhost admin]# cd ..
[root@localhost home]# pwd
/home
```

### 3.4 查看文本内容--cat，tail
查看文件内容的命令其实还有很多，比如tac，nl，more，less等等，但就我们平常使用而言这两个就足够了，再不够下一节点的那两个也可以临时用作文本查看。  

#### 3.4.1 cat
同样的，cat命令也可以使用`cat --help`查看帮助说明，不过平时使用的话可能最多会使用到`-n`：
```
-n, --number             对输出的所有行编号
```
使用示例：
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
tail翻译成中文是"尾巴"的意思，所以从名字就可以看出来它的作用，这个命令表示从文档结尾处开始查看，其选项如下：
```
-n, --lines=K            n是具体的数字，显示最后n行
-f, --follow            持续检测文件的输出文本(很适合跟踪实时日志)
```
使用示例：
```
[root@study ~]# tail -10 aaa.txt       #显示最后10行
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

[root@study ~]# tail -f aaa.txt 		实时跟踪显示
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
由于`tail -f`的持续输出，会导致我们的界面一直在文本的输出状态，如果这时候要退出，那么按`[Ctrl]+C`即可。

### 3.5 编辑文本--nano，vi
文本编辑是经常会用到的功能，这两个命令本身还是有些复杂，不过我们通常只是简单地使用，毕竟在命令窗口下编辑文本还是有些麻烦，所以可以将文件下载下来进行修改，然后再上传上去覆盖。但是如果只是简单的增减内容，倒是可以直接就用它们俩了。
#### 3.5.1 nano

虽然我们使用`nano -h`可以取得说明信息，但是基本上用不上那些选项..倒是一些快捷键需要知道：  

![](https://github.com/JustNeedOneMoreStep/LinuxDocs/blob/master/images/nano.png?raw=true)  

如图，当前已经进入到`aaa.txt`这个文件里面了。可以看到，下方很多`^G`这样的功能，这里`^`代表的是`Ctrl`键，`^G`就是指`Ctrl+G`。  
之后我们可以使用键盘上的`↑↓←→`移动光标，移动到指定位置，输入我们需要的信息，最后既可以使用`^O`的方式先保存，然后`^X`退出，也可以直接`^X`退出，系统提示保存：

```
储存更动过的缓冲区吗(回答 "No" 会撤销修改)？                                                                      
 Y 是
 N 否           ^C 取消
```
输入Y，然后提示：
```
要写入的文件名: aaa.txt                                                                                           
^G 求助                     M-D DOS 格式                M-A 附加                    M-B 备份文件
^C 取消                     M-M Mac 格式                M-P 前引
```
下方的按键都不需要在意，在这里如果想取消保存并返回编辑就按`^C`，否则就检查文件名，如果要覆盖直接按`[Enter]`键确认即可，重命名会有其他选项，自己操作时看文字就明白，就不再细说了。

#### 3.5.2 vi

vi命令是Linux系统默认会调用的文本编辑器，它的进阶版有vim，相比之下增加了一些功能，不过基础使用是一致的。vi比nano强大多了，并分为三种工作模式，分别是命令模式、文本输入模式、和末行模式。  

- **命令模式**：vi打开文件后默认所处的模式，同时如果在其他模式中，按下`[Esc]`也可以返回到这种模式。  
常用功能：
```
ndd：删除当前光标所在行及其后n-1行
/pattern：从光标开始处向后搜索pattern(pattern是要搜索的内容)
?pattern：从光标开始处向前搜索pattern
n：在同一方向重复上一次搜索命令(使用/pattern就是查找下一个)
N：在反方向上重复上一次搜索命令
```
第一个需要说明下，`n`是我们按下的一个数字，然后连按两下`d`键就可以删除内容了，如果只按两下`d`则只删除当前光标所在行。  
`/pattern`和`?pattern`则如下，控制台先输入`vi linux.txt`：
```
linux 操作系统的诞生
  ...
  2001年1月，Linux 2.4发布，它进一步地提升了SMP系统的扩展性，同时它也集成了很多用于支持桌面系统的特性：USB，PC卡（PCMCIA）的支持，内置的即插即用，等等功能。
  2003年12月，Linux 2.6版内核发布，相对于2.4版内核2.6在对系统的支持都有很大的变化。
  2004年的第1月，SuSE嫁到了Novell，SCO继续顶着骂名四处强行“化缘”， Asianux， MandrakeSoft也在五年中首次宣布季度赢利>。3月，SGI宣布成功实现了Linux操作系统支持256个Itanium 2处理器。
~                                                                                                                 
"linux.txt" 18L, 3041C
```
然后按下"/"或"?"，在末行位置就会像下面这样显示：
```
linux 操作系统的诞生
  ...
  2001年1月，Linux 2.4发布，它进一步地提升了SMP系统的扩展性，同时它也集成了很多用于支持桌面系统的特性：USB，PC卡（PCMCIA）的支持，内置的即插即用，等等功能。
  2003年12月，Linux 2.6版内核发布，相对于2.4版内核2.6在对系统的支持都有很大的变化。
  2004年的第1月，SuSE嫁到了Novell，SCO继续顶着骂名四处强行“化缘”， Asianux， MandrakeSoft也在五年中首次宣布季度赢利>。3月，SGI宣布成功实现了Linux操作系统支持256个Itanium 2处理器。
~     
				   #原来的"linux.txt" 18L， 3041C没有了
/                  #按下"/"或"?"后，该行会出现光标等待输入
				   #然后输入要搜索的关键字后敲[Enter]即可
```

- **文本输入模式**：vi进入文件后，按下`a`，`i`，`o`等就可以进入文本输入模式，常用的按键如下：
```
i ：在光标前开始编辑
I ：在当前行首开始编辑
a ：光标后开始编辑
A ：在当前行尾开始编辑
o ：在当前行之下新开一行开始编辑
O ：在当前行之上新开一行开始编辑
```

- **末行模式**：使用vi进入文件后，按下`:`即可输入末行模式。常用的末行模式命令如下：
```
:w ：保存当前文件
:x ：保存当前文件并退出vi，等同于:wq
:q ：退出vi
:q! ：不保存文件并强制退出vi
```  

如果想要了解更多vi的用法，可以参考[Linux文件编辑命令详细整理](http://blog.csdn.net/u013142781/article/details/50735470)

### 3.6 创建目录--mkdir
文件我们已经会修改了，那么下面就讲讲怎么创建目录吧。  
`mkdir`有两个常用选项：
```
-m, --mode=MODE   设置文件权限(和chmod一样)
-p, --parents     创建多级目录，若父目录不存在则自动创建
```
之前讲过权限的概念，不知道大家还有没有印象，这里-m就是在创建文件夹的时候手动赋予权限等级，如果没有该参数，则自动使用系统默认值(普通用户一般为rwxrwxr-x，而root一般为rwxr-xr-x，默认值是可修改的)，下面看一个示例：
```
[admin@study test]$ mkdir testdir
[admin@study test]$ ls -l
总用量 0
drwxrwxr-x. 3 admin project 17 6月  19 06:26 mydir

#多级目录
[admin@study test]$ mkdir /mydir/aaa/bbb/ccc
mkdir: 无法创建目录"/mydir/aaa/bbb/ccc": 没有那个文件或目录
[admin@study test]$ mkdir -p mydir/aaa/bbb/ccc
[admin@study test]$ ll
总用量 0
drwxrwxr-x. 3 admin project 17 6月  19 06:26 mydir
drwxrwxr-x. 2 admin project  6 6月  19 06:25 testdir
[admin@study test]$ cd mydir/aaa/bbb/ccc
[admin@study ccc]$ pwd
/test/mydir/aaa/bbb/ccc

#带权限创建目录
[admin@study test]$ mkdir -m 766 testdir2
[admin@study test]$ ls -l
总用量 0
drwxrwxr-x. 3 admin project 17 6月  19 06:26 mydir
drwxrwxr-x. 2 admin project  6 6月  19 06:25 testdir
drwxrw-rw-. 2 admin root     6 6月  19 06:37 testdir2
```
可以看到，创建多级目录时，如果不带`-p`，并且中间有目录不存在时，就会报错。

### 3.7 删除文件/目录--rm
刚才创建过的没有意义的目录，留在系统很会影响我们的浏览，那么我们使用`rm`就把它们删掉吧。  
`rm`的常用选项如下：  
```
-f, --force           强制删除，忽略不存在的文件，不显示警告信息
-i                    在每个目录删除前都询问是否删除
-r, -R, --recursive   删除该目录及其所有子文件和目录
```
这里要注意的是，如果删除目录的话，必须使用`-r`，默认只能删除文件，使用示例：
```
[admin@study test]$ ls
mydir  testdir  testdir2

[admin@study test]$ rm testdir2/
rm: 无法删除"testdir2/": 是一个目录

[admin@study test]$ rm -rf testdir2
[admin@study test]$ ls
mydir  testdir
```
> 提一下，如果目录下没有任何文件，也就是空目录的话，也可以使用`rmdir`这个命令来删除。

### 3.8 修改权限--chmod
之前介绍过权限的概念，那么这里就详细说一下怎么修改文件和目录的权限，其用法如下：
```
c[admin@study test]$hmod [OPTION]... MODE[,MODE]... FILE...
```
[OPTION]可以暂时不用，需要用时使用`chmod --help`查看即可，这里给几个示例：
```
#先创建1个文件以作示范
[admin@study test]$ touch test.txt        #touch 创建空文件
[admin@study test]$ ll
总用量 0
-rw-rw-r--. 1 admin root 0 6月  19 10:22 test.txt

[admin@study test]$ chmod 421 test.txt
[admin@study test]$ ll
dr---w---x. 2 admin root 6 6月  19 10:22 test.txt

[admin@study test]$ chmod 666 test.txt
[admin@study test]$ ll
drw-rw-rw-. 2 admin root 6 6月  19 10:22 test.txt

[admin@study test]$ chmod u=rw,g=r,o=r test.txt
[admin@study test]$ ll
-rw-r--r--. 1 admin root 0 6月  19 10:22 test.txt

[admin@study test]$ chmod u+x,g+wx test.txt
[admin@study test]$ ll
-rwxrwxr--. 1 admin root 0 6月  19 10:22 test.txt

[admin@study test]$ chmod ugo=rwx test.txt
[admin@study test]$ ll
-rwxrwxrwx. 1 admin root 0 6月  19 10:22 test.txt
```
之前也提到过`u`就是`owner`，`g`就是`group`，`o`就是`others`，那么像`u=rw`这种形式的，就是直接指定owner的权限为`rw`，而像`u+x`这种的，是在owner已有权限的基础上再增加`x`权限。改变权限时，只需要指定至少一种角色即可。

### 3.11 查询进程--ps
上面说了那么多和文件目录相关的命令，而在实际使用过程中，查询系统进程的命令也会经常用到，比如，我们经常需要观察tomcat或weblogic是否启动，oracle、nginx是否已启动等等。事实上，`ps`的使用是比较复杂的，所以这里就介绍下常用的`ps`选项：
```
-e          显示所有进程
-f          显示详细信息
```
使用效果如下：
```
[root@localhost /]# ps -ef
UID        PID  PPID  C STIME TTY          TIME CMD
root         1     0  0 Jun24 ?        00:00:00 /sbin/init
root         2     0  0 Jun24 ?        00:00:00 [kthreadd]
root         3     2  0 Jun24 ?        00:00:00 [migration/0]
root         4     2  0 Jun24 ?        00:00:00 [ksoftirqd/0]
root         5     2  0 Jun24 ?        00:00:00 [stopper/0]
...
root     22454     1  0 08:42 ?        00:00:00 nginx: master process nginx
nginx    22455 22454  0 08:42 ?        00:00:00 nginx: worker process
root     22517 22331  0 09:18 pts/5    00:00:00 ps -ef
```
其每一列的对应关系如下：  

UID | PID | PPID | C | STIME | TTY | TIME | CMD
---|---|---|---|---|---|---|---
执行用户 | 进程ID | 父进程ID | CPU使用百分比 | 程序启动时间 | 终端机位址 | 运行时长 | 命令名称

这么一大堆，其实依据我们的使用就只是关心下有没有这个程序，这个程序的进程ID是多少怎样罢了，对应的也就是`PID`，`CMD`这两列，其余的信息如果想进一步了解的请百度。后一个都好说，因为一个Linux的每一个进程都是有一个唯一ID标识的，但是我们要怎么在这么一大堆进程里面看到我们的进程在运行没有呢?这里需要介绍另一个命令`grep`查询命令。  
```
[root@localhost /]# grep [OPTIONS] pattern [FILE]
```
这里，pattern是指的正则表达式，同时我们暂时不需要使用到选项，或者是在某个文件中查找，所以只是很简单的添加了个pattern文本。例如，我们想要查看nginx是否启动，那么可以这样执行：
```
#nginx没启动时
[root@localhost /]# ps -ef | grep nginx
root     22526 22331  0 09:27 pts/5    00:00:00 grep nginx

#nginx启动
[root@localhost /]# nginx
[root@localhost /]# ps -ef | grep nginx
root     22454     1  0 08:42 ?        00:00:00 nginx: master process nginx
nginx    22455 22454  0 08:42 ?        00:00:00 nginx: worker process
root     22521 22331  0 09:24 pts/5    00:00:00 grep nginx
```
这里，我们在`ps -ef`后又使用了`|`来连接`grep nginx`。那么`|`的作用是什么呢?
```
|：将前一个命令得到的结果传递给下一个命令
```
也就是说，我们把`ps -ef`查询出来的结果(实际上，查询结果也是一堆文本)，传递给了`grep`命令来执行，`grep`后面跟的就是要查询文本的正则表达式，这样我们就可以查到nginx相关的进程。可以看到，当nginx没有启动的时候，输出信息中只有`grep nginx`这个命令在执行，当nginx启动后，就能查询到了。

### 3.12 结束进程--kill
上一节中我们获取了nginx的相关进程信息，那么假如现在我们需要停止nginx，但是正常的停止命令执行无效，这个时候就只能直接用终止进程的命令`kill`来暴力终结了，而`kill`命令需要使用到`PID`这个参数，所以这也就是为什么我们之前需要获取进程信息的原因之一了。先介绍下`kill`吧：
```
kill -signal PID
```
其中，`signal`是kill命令在终止进程时需要传递给该进程的信号，因为在Linux中，程序的相互管理就是通过给该程序一个信号(signal)去告诉它们要做什么，这里可以使用`kill -l`查询到所有的signal，不过我们不需要用到那么多，常用的有下面几种：
```
1	SIGHUP	启动被終止的程序，可让该PID重新读取自己的设置，类似重新启动
2	SIGINT	相当于用键盘 [Ctrl]+C 來中断一個程序的進行
9	SIGKILL	強制中断一個程序的运行
15	SIGTERM	以正常的结束程序来终止程序。由于是正常的终止， 所以后续的动作也会执行完全。但是如果是程序本身出问题了，那么执行这个signal也是没有作用的。
```
好了，不知道大家发现没有，这里面最最常用的，其实就是`9`这个值了，因为一般的程序，都是可以通过它们自身直接终止与重启的，所以一般也就不需要kill命令，我们使用kill命令，大多也是由于某个程序出了问题的时候。那么，现在来把nginx终掉吧：
```
[root@localhost /]# ps -ef | grep nginx
root     22531     1  0 09:48 ?        00:00:00 nginx: master process nginx
nginx    22532 22531  0 09:48 ?        00:00:00 nginx: worker process
root     22534 22331  0 09:48 pts/5    00:00:00 grep nginx
[root@localhost /]# kill -9 22531
[root@localhost /]# kill -9 22532       #由于nginx有多个用户进程，所以需要全部kill掉才行

[root@localhost /]# ps -ef | grep nginx
root     22538 22331  0 09:49 pts/5    00:00:00 grep nginx
```

### 3.13 查看系统状态--top
由于之前在成都中心的时候，经常碰到内存占用率超高，而导致weblogic等程序运行缓慢等问题,于是就使用`top`命令来查看系统的状态：
```
[root@localhost /]# top
top - 10:02:40 up 32 days, 21:17,  6 users,  load average: 0.00, 0.00, 0.00
Tasks:  96 total,   1 running,  95 sleeping,   0 stopped,   0 zombie
Cpu(s):  0.0%us,  0.0%sy,  0.0%ni,100.0%id,  0.0%wa,  0.0%hi,  0.0%si,  0.0%st
Mem:    518340k total,   498484k used,    19856k free,    78380k buffers
Swap:  1048572k total,   460084k used,   588488k free,   186648k cached

  PID USER      PR  NI  VIRT  RES  SHR S %CPU %MEM    TIME+  COMMAND                                                             
    1 root      20   0 21276  460  280 S  0.0  0.1   0:00.53 init                                                                 
    2 root      20   0     0    0    0 S  0.0  0.0   0:00.02 kthreadd                                                             
    3 root      RT   0     0    0    0 S  0.0  0.0   0:00.00 migration/0                                                          
    4 root      20   0     0    0    0 S  0.0  0.0   0:00.82 ksoftirqd/0                                                          
    5 root      RT   0     0    0    0 S  0.0  0.0   0:00.00 stopper/0                                                            
    6 root      RT   0     0    0    0 S  0.0  0.0   0:02.69 watchdog/0                                                           
    7 root      20   0     0    0    0 S  0.0  0.0   4:08.60 events/0      
    ...
```
具体的含义，可以到[linux怎样使用top命令查看系统状态](https://jingyan.baidu.com/article/4d58d5412917cb9dd4e9c0ed.html)这个网址去看。这里我只介绍下，怎么把top的监控结果输出到文本中去：
```
[root@localhost test]# top > /test/top.txt
```
上面的语句中，`>`是指把结果写入到`/test/top.txt`这个文件里面去并且覆盖原内容，而对应的，使用`>>`可以将执行结果追加到原内容后面而不是覆盖。其实在这里很容易就可以知道，其他命令也可以用这种方式将结果写入到文本中，当然，假如我们每隔多久多久就手动去执行一次这个命令，那真是够累的..所以这时候就可以搭配`crontab`命令用定时任务来执行，这里就不作过多介绍了，有兴趣的小伙伴请百度。

### 3.14 查看磁盘空间使用情况--df
有段时间开发网厅，用ftp上传文件老是失败，百思不得其解，后来删除了一些文件发现又可以上传成功了，之后才发现上传文件保存的地方原来在系统文件目录下，而系统空间本身就划分得比较小，也因此导致那个硬盘空间被占满了..所以没事的时候大家就可以用`df`命令观察下磁盘使用情况：
```
[root@localhost test]# df -h
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda2        11G  4.0G  6.1G  40% /
tmpfs           254M     0  254M   0% /dev/shm
/dev/sda1       291M   84M  193M  31% /boot
```
`-h`在介绍`ls`命令时就使用了，这里的意思是一样的，不过`df`命令还有其他的显示单位：
```
-k  ：统一以 KBytes 的容量显示各文件系统
-m  ：统一以 MBytes 的容量显示各文件系统
```
那么在这里，前面的Filesystem我们就不太需要关心了，我们关心的是后面的几个参数  

Size | Used | Avail | Use | Mounted on
---|---|---|---|---
磁盘总大小 | 已用空间 | 剩余可用空间 | 使用百分比| 挂载点

所谓的挂载点，你可以当成我们平常使用的目录，`/dev/shm`是内存虚拟的磁盘空间，而`/boot`则是开机文件所在目录，这两个也和我们没有关系，因为我们的文件都在`/`下面..当然，有时候也会有`/home`目录，这就需要根据实际情况自己去查看了。

### 3.15 上传/下载--rz，sz
rz，sz是Linux/Unix同Windows进行ZModem文件传输的命令行工具。至于什么是ZModem，请百度..不过这两个命令不是Linux自带的，如果服务器上没有，那么需要自己的进行安装，安装方式的话...也请自行百度。

#### 3.15.1 rz
有的时候电脑上没有ftp，但是又想要直接传文件给Linxu服务器，那么使用`rz`命令可以很轻松地完成。`rz`有很多选项，但是我们平常只需要使用`-be`这两个选项就够了，目的是为了上传到服务端的文件内容与原始文件保持一致。如果有重名文件，那么加上`-y`可以对文件进行覆盖。
```
-b, --binary                以二进制的方式传输
-e, --escape                对控制字符进行转义
-y, --overwrite             如果有重名文件，则覆盖
```

使用示例：

```
[root@localhost ~]# cd /test        #先切换到要上传的目录
[root@localhost test]# rz -be
```
此命令执行时，会弹出文件选择对话框，选择好需要上传的文件之后，点确定，就可以开始上传的过程了。  

#### 3.15.2 sz
使用sz则可以快速下载文件
```
[root@localhost test]# sz filename
```
同样，执行后会弹出目录选择框，选择目录点确定即可。

> 最后提一句，其实，XShell带有XFtp这个工具，可以在可视化的界面下进行上传和下载，也十分的方便，而且ftp比ZModem传输速度快..

### 3.16 远程登录--ssh
根据[SSH协议介绍](http://blog.csdn.net/macrossdzh/article/details/5691924)：
> SSH是英文Secure Shell的简写形式。通过使用SSH，你可以把所有传输的数据进行加密，这样"中间人"这种攻击方式就不可能实现了，而且也能够防止DNS欺骗和IP欺骗。使用SSH，还有一个额外的好处就是传输的数据是经过压缩的，所以可以加快传输的速度  

简单来说，使用SSH登录可以加强我们系统的安全性，加快传输速度。不知道大家有没有发现，在XShell登录的时候，在页面上有这样的文字：
```
ssh://root:***********@23.83.251.102:28078

ssh://root@23.83.251.102:28078
```
其实，我们配置的XShell登录就是用的SSH。不过这里面我们介绍的是linux内部的命令`ssh`，说远了..Linux中ssh的功能也有很多，所以这里只介绍怎么使用ssh连接远程主机：
```
ssh name@remoteserver
ssh remoteserver -l name
ssh name@remoteserver -p port
ssh remoteserver -l name -p port
```
`name`是登录用户名，`-l`后接上登录名，`-p`后接端口号，不写`-p`则默认是22，如果端口有修改，就带上吧，比如示例中的`28078`这个：
```
[root@localhost ~]# ssh admin@23.83.251.102 -p 28078
admin@23.83.251.102's password: 
#密码输入正确后
Last login: Thu Jul 27 20:45:57 2017 from 182.149.66.15
[admin@localhost ~]$ 
```

## 4.windows下检测Linux主机的网络及端口--ping/telnet
有的时候，Linux系统会登录不上去，但是不知道具体的原因，那么一般可以先从网络开始检查。
### 4.1 ping
在windows系统下测试下网络是否有响应，来确定是否是网络的问题，可以使用dos下的`ping`命令。ping属于一个通信协议，是TCP/IP协议的一部分，而且该命令在Unix以及Linux下也都支持哦。windows下，直接按快捷键[Win]+R打开运行窗口，输入`cmd`打开命令窗口，然后执行`ping`：
```
#ping成功的示例
C:\Users\Darkness1m>ping www.yinhai.com

正在 Ping www.yinhai.com [125.71.203.198] 具有 32 字节的数据:
来自 125.71.203.198 的回复: 字节=32 时间=5ms TTL=59
来自 125.71.203.198 的回复: 字节=32 时间=6ms TTL=59
来自 125.71.203.198 的回复: 字节=32 时间=7ms TTL=59
来自 125.71.203.198 的回复: 字节=32 时间=5ms TTL=59

125.71.203.198 的 Ping 统计信息:
    数据包: 已发送 = 4，已接收 = 4，丢失 = 0 (0% 丢失)，
往返行程的估计时间(以毫秒为单位):
    最短 = 5ms，最长 = 7ms，平均 = 5ms
    
#ping不通的示例    
C:\Users\Darkness1m>ping www.cdzfgjj.gov.cn

正在 Ping www.cdzfgjj.gov.cn [182.150.40.151] 具有 32 字节的数据:
请求超时。
请求超时。
请求超时。
请求超时。

182.150.40.151 的 Ping 统计信息:
    数据包: 已发送 = 4，已接收 = 0，丢失 = 4 (100% 丢失)，
```
可以看到，ping网址的时候，会自动将该网址对应的ip地址一并给显示出来。这里，ping其他网址有响应而ping指定的网址超时的时候，说明这个网址可能设置了安全限制，也可能是有问题，需要那边的网络管理员进行检查。

### 4.2 telnet
telnet和ping一样，也是TCP/IP协议族中的一员，是Internet远程登陆服务的标准协议和主要方式。我们平常使用telnet，都是为了测试目标机器的TCP端口是否开通了，但是如果连接失败，则可能是防火墙屏蔽了该端口，这时候也需要通知网路管理员进行检查。
```
C:\Users\Darkness1m>telnet www.baidu.com 21
正在连接www.baidu.com...无法打开到主机的连接。 在端口 21: 连接失败

C:\Users\Darkness1m>telnet 23.83.251.102 28078
```
网址和端口之间需要用空格隔开。用这种方式，如果成功，则在屏幕上显示一片黑。详情可以参考[telnet用法 测试端口号](https://jingyan.baidu.com/article/a3aad71aa9e6efb1fb009694.html)

## 5.tomcat的安装
对于不同发行版而言，Tomcat的安装，其实都是大同小异。当然，最好还是按照自己Linux的发行版本在网上去搜索相对应的安装教程。使用`cat /etc/issue`命令可以查看自己的版本：
```
[root@localhost test]# cat /etc/issue
CentOS release 6.9 (Final)
Kernel \r on an \m
```
可以看到，我是centos 6.9 的系统版本。  
Tomcat的安装，大致分为下面几个步骤：  

1. 去官网下载jdk(.rpm)和tomcat(.tar.gz)包(注意对应系统是32位还是64位)，然后上传jdk-xxxx-linux-x64.rpm及apache-tomcat-x.x.xx.tar.gz  
> 注1：rpm是CentOS，Red Hat等以rpm方式管理的软件安装包，类似于windows下的exe  
> 注2：tar.gz是一种压缩包格式文件，gz代表以gzip方式压缩  

2. 使用`yum localinstall 路径/jdk-xxxx-linux-x64.rpm -y`或`rpm -ivh 路径/jdk-xxxx-linux-x64.rpm`安装jdk
3. 使用alternatives配置java
```
[root@study ~]# alternatives --install <链接> <名称> <路径> <优先级>

[root@study ~]# alternatives --install /usr/bin/java java /安装路径/bin/java 10086
[root@study ~]# alternatives --install /usr/bin/javac javac /安装路径/bin/javac 10086

[root@study ~]# alternatives --config java
共有 2 个提供“java”的程序。

  选项    命令
-----------------------------------------------
*  1           java-1.8.0-openjdk.x86_64 (/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.102-4.b14.el7.x86_64/jre/bin/java)
 + 2           /usr/java/jdk1.7.0_80/bin/java
按 Enter 保留当前选项[+]，或者键入选项编号：2    #选择需要的jdk版本
```
安装后，使用`java -version`查看jdk版本
```
[root@study ~]# java -version
java version "1.7.0_80"
Java(TM) SE Runtime Environment (build 1.7.0_80-b15)
Java HotSpot(TM) 64-Bit Server VM (build 24.80-b11, mixed mode)
```

4. 安装tomcat
```
#解压tomcat
[root@study ~]#  tar -zxvf apache-tomcat-x.x.xx.tar.gz -C /usr/local/tomcat
#启动tomcat
[root@study ~]# /usr/local/tomcat/apache-tomcat-7.0.79/bin/startup.sh
```
> `tar`是Linux中的压缩/解压命令，其选项`z`指`使用gzip压缩的文件`，`x`指`操作为解压`，`v`指`解压时显示所有文件`，`f`后跟`要解压的文件名`，`C`后跟`指定的解压目录`，不指定`C`则默认为当前目录

5. 配置tomcat内存
打开tomcat的bin目录下文件catalina.sh，在`cygwin=false`这句话前面前：
```
# OS specific support. $var _must_ be set to either true or false.
# 添加下面这一句
export JAVA_OPTS="-Xms512m -Xmx1024m -XX:PermSize=128m -XX:MaxPermSize=256m"
cygwin=false
```  

参考链接：

- [CentOS下安装JDK笔记](http://www.linuxidc.com/Linux/2015-01/111414.htm)
- [CentOS 7默认的jdk 1.7升级方法(到1.8) - alternatives的功能](http://blog.csdn.net/evandeng2009/article/details/50145701)
> 这篇文章中少介绍了一步使用`alternatives --install`安装java配置项的步骤，导致`alternatives --config`找不到我们新安装的java版本，该步骤可参考--[Linux：多个 jdk 的安装和管理 update-alternatives , 或 alternatives](http://blog.csdn.net/netmicrobe/article/details/50440830)
- [Centos6.5安装tomcat](http://www.cnblogs.com/cldct/articles/5446948.html)
- [centos7下安装tomcat7](http://www.cnblogs.com/xhkj/p/6545241.html)

## 6.网址推荐
- [为什么要学习 Linux？](https://www.zhihu.com/question/20117703)
- [鳥哥的 Linux 私房菜](http://linux.vbird.org)
- [Linux-软件包管理-wget,rpm,yum,apt-get](http://blog.csdn.net/alexdamiao/article/details/51473713)
- [腾讯云开发者实验室](https://cloud.tencent.com/developer/labs)
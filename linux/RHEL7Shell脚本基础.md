## 何为shell
>在计算机科学中，Shell俗称壳（用来区别于核），是指“提供使用者使用界面”的软件（命令解析器）  
它类似于DOS下的command和后来的cmd.exe。它接收用户命令，然后调用相应的应用程序  
同时它又是一种程序设计语言:  
作为命令语言，它交互式解释和执行用户输入的命令或者自动地解释和执行预先设定好的一连串的命令  
作为程序设计语言，它定义了各种变量和参数，并提供了许多在高级语言中才具有的控制结构，包括循环和分支  

在一些复杂的Linux维护工作中,大量重复的输入和交互操作不但费时费力,而且容易出错  
编写shell脚本能进行批量的处理,自动化的完成维护,减轻管理员的负担  
linux的shell脚本是一种特殊的应用程序，常见的shell解释器有很多种使用不同的shell时期内部指令命  
使用下面的命令查看系统的shell解释器:
```
[root@VM_200_13_centos ~]# chsh -l
/bin/sh
/bin/bash
/sbin/nologin
/usr/bin/sh
/usr/bin/bash
/usr/sbin/nologin
/bin/tcsh
/bin/csh
[root@VM_200_13_centos ~]# cat /etc/shells 
/bin/sh
/bin/bash
/sbin/nologin
/usr/bin/sh
/usr/bin/bash
/usr/sbin/nologin
/bin/tcsh
/bin/csh
```
## 第一个shell脚本
> /bin/bash是大多数linux中默认的shell解释器，之后的所有脚本的编写都是bash脚本

### 编写
编写一个输出两行信息的shell脚本example.sh
linux不以后缀名区分文件，但为了便于区分则以.sh为后缀表示为shell脚本
```shell
#!/bin/bash
#example shell script
echo "this is an example for shell script"
echo "system will print two line messages on screen"
```
### 执行方式
| 执行方式 | 说明 |
| :------------- | :------------- |
| 使用绝对路径执行 | 需要执行权限 |
| 使用相对路径执行 | 需要执行权限 |
| sh命令执行 | 不需要执行权限<br>使用-x选项还会输出执行过程 |
| . 脚本名 | 不需要执行权限|
| source 脚本名 | 不需要执行权限<br>主要用于生效配置文件 |
查看example.sh脚本的执行权限
```
[root@VM_200_13_centos ~]# ll example.sh 
-rw-r--r-- 1 root root 130 Aug 31 10:09 example.sh
```
授予其执行权限
```
[root@VM_200_13_centos ~]# ll example.sh 
-rwxr-xr-x 1 root root 130 Aug 31 10:09 example.sh
```
执行
```
[root@VM_200_13_centos ~]# ./example.sh 
this is an example for shell script
system will print two line messages on screen
```
## 变量与特殊变量应用
脚本中最重要的东西就是变量  
变量的定义是：可以存放一个可变的值的空间  
可以通过不同的环境进行改变就是一个可以变的值  
默认情况下: 在Linux中可以将每个shell看成不同的执行环境,所以相同的一个变量名称在不同的变量执行环境中的变量值是不同的 
常见的shell变量分类：自定义变量、环境变量、位置变量、预定义变量  
变量的输出语法：echo $变量名  

### 自定义变量
>自定义变量是用户根据自己的环境自己定义的变量，Bash中比较简单的变量  
不用进行提前声明，而是直接指定变量名称并赋给初始值  

定义变量的基本格式为：变量名=变量值  
定义变量的规范：

1. 以字母和下划线开头
2. 中间不能有空格，可以使用下划线
3. 不能使用标点符号
4. 不能使用bash里的关键字，可用help命令查看保留关键字

调用变量使用$进行调用
```
[root@VM_200_13_centos ~]# user=sakuraph
[root@VM_200_13_centos ~]# _user_job=singleDog
[root@VM_200_13_centos ~]# system=rhel7.2
[root@VM_200_13_centos ~]# echo $user $_user_job $system
sakuraph singleDog rhel7.2
```
当变量名和后面的字符容易混淆的时候应该使用{}将变量名括起来
```
[root@VM_200_13_centos ~]# echo ${system}linux
rhel7.2linux
```
变量值中包含括号需要用双引号将变量值包裹起来
```
[root@VM_200_13_centos ~]# message="this is a message"
[root@VM_200_13_centos ~]# echo $message
this is a message
```
在双引号的范围内还可以引用其他的变量，从而能够将现有的变量赋值给新的变量
```
[root@VM_200_13_centos ~]# text="show :$message"
[root@VM_200_13_centos ~]# echo $text
show :this is a message
```
当要赋值的内容包括"$"、"\"等,具有其他含义的特殊字符时,应使用单引号将其括起来  
在单引号范围内将无法引用其他的值，任何字符均作为普通字符看待，但赋值的内容包含单引号时需要使用\’符号进行转义以免冲突 
```
[root@VM_200_13_centos ~]# msg=\'mesaage:'$ is money,\ is devide'\'
[root@VM_200_13_centos ~]# echo $msg
'mesaage:$ is money,\ is devide'
```
使用反撇号将某个命令的屏幕输出结果赋值给变量
```
[root@VM_200_13_centos ~]# rs=`which pwd`
[root@VM_200_13_centos ~]# rpm -qf $rs
coreutils-8.22-15.el7.x86_64
[root@VM_200_13_centos ~]# rpm -qf `which pwd`
coreutils-8.22-15.el7.x86_64
```
反撇号中不能进行命令的嵌套，反撇号括起来的范围内必须是可执行的命令,否则将会出现错误
```
[root@VM_200_13_centos ~]# rpm -q `rpm -qf `which pwd``
rpm: no arguments given for query
which-2.20-7.el7.x86_64
package pwd is not installed
```
对于上面的情况则使用括号代替反撇号
```
[root@VM_200_13_centos ~]# rpm -q $(rpm -qf $(which pwd))
coreutils-8.22-15.el7.x86_64
```
除了上面的赋值之外还可以使用read命令进行赋值  
read命令用来提示用户输入信息，从而实现简单的交互式过程（其实我们所输入的命令就是一种交互式的过程）  
执行时需要从标准输入设备键盘读取一行，并以空格为分隔符
```
[root@VM_200_13_centos ~]# read system kernel
Redhat Enterprise Linux 3.10
[root@VM_200_13_centos ~]# echo $system $kernel 
Redhat Enterprise Linux 3.10
```
为了交互式更加的形象，提高易用性，加上 -p选项来设置提示信息
```
[root@VM_200_13_centos ~]# read -p "please input your username:" username
please input your username:sakuraph
[root@VM_200_13_centos ~]# echo $username
sakuraph
```
## 局部变量与全局变量
>在当前bash下声明的bash变量为局部变量，只在当前bash环境下有效  
切换到其他控制台或者bash下则失效

在当前bash环境下声明一个变量再切换至子shell会发现在变量已经失效
```
[root@VM_200_13_centos ~]# temp="this is a temp var"
[root@VM_200_13_centos ~]# echo $temp 
this is a temp var
[root@VM_200_13_centos ~]# bash
[root@VM_200_13_centos ~]# echo $temp

[root@VM_200_13_centos ~]# 

```
| 命令 | 说明 | 区别 |
| :------------- | :------------- | :------------- |
| set | 用来显示本地变量 | 显示当前shell的变量，包括当前用户的变量 |
| env | 用来显示环境变量 | 显示当前用户的变量 |
| export | 用来显示和设置环境变量 | 显示当前导出成用户变量的shell变量 |

使用export将一个变量设置为全局变量
```
[root@VM_200_13_centos ~]# Linux=7.2
[root@VM_200_13_centos ~]# linux=6.5
[root@VM_200_13_centos ~]# echo $Linux $linux
7.2 6.5
[root@VM_200_13_centos ~]# export Linux linux
[root@VM_200_13_centos ~]# bash
[root@VM_200_13_centos ~]# echo $Linux $linux
7.2 6.5
```
也可以使用export定义一个全局变量并赋值
```
[root@VM_200_13_centos ~]# export site=www.baidu.com
[root@VM_200_13_centos ~]# echo $site
www.baidu.com
[root@VM_200_13_centos ~]# bash
[root@VM_200_13_centos ~]# echo $site
www.baidu.com
[root@VM_200_13_centos ~]# exit
exit
```
## 数值变量的运算
shell脚本的数值运算多用于脚本程序的过程控制（如循环次数，使用量比较等）  
在shell环境中，只能进行比较简单的整数运算  
运算符与变量之间必须有一个空格位，整数的运算主要是通过内部命令expr进行运算  
格式  变量1 运算符 变量2  
其中 变量1 、变量2 ……对应的需要计算的数值变量（需要$符号调用）  
常用的几种运算符如下所示：  
加法运算：+ 
减法运算：- 
乘法运算：\* （需要转义）
除法运算：\
求模运算：%
```
[root@VM_200_13_centos ~]# a=100
[root@VM_200_13_centos ~]# b=33
[root@VM_200_13_centos ~]# expr $a % $b
1
[root@VM_200_13_centos ~]# expr 10 / 5
2
[root@VM_200_13_centos ~]# expr $a + $b
133
[root@VM_200_13_centos ~]# expr $a - $b
67
[root@VM_200_13_centos ~]# expr $b - $a
-67
[root@VM_200_13_centos ~]# expr $a / $b
3
[root@VM_200_13_centos ~]# expr $a * $b
expr: syntax error
[root@VM_200_13_centos ~]# expr $a \* $b
3300
```
若要将运算结果赋值给其他变量可以这么做
```
[root@VM_200_13_centos ~]# c=$(expr $a \* $b)
[root@VM_200_13_centos ~]# echo $c
3300
```
## 环境变量
环境变量是指系统本身运行需要由linux系统提前创建好的一类变量  
主要用于用户的工作环境，包括(用户的宿主目录，命令的查找路径，用户的当前目录，登录的终端等)  环境变量的值由操作系统本身自己维护，随着用户的状态改变而改变

环境变量的配置文件在/etc/profile（全局）
用户宿主目录/home/zhangsan/.bash_profile（局部）

使用env调取当前环境变量
```
[root@VM_200_13_centos ~]# env
XDG_SESSION_ID=49460
HOSTNAME=VM_200_13_centos
TERM=xterm
SHELL=/bin/bash
HISTSIZE=3000
SSH_CLIENT=182.150.21.5 10175 22
SSH_TTY=/dev/pts/2
USER=root
Linux=7.2
LS_COLORS=rs=0:di=01;34:ln=01;36:mh=00:pi=40;33:so=01;35:do=01;35:bd=40;33;01:cd=40;33;01:or
=40;31;01:mi=01;05;37;41:su=37;41:sg=30;43:ca=30;41:tw=30;42:ow=34;42:st=37;44:ex=01;32:*.ta
r=01;31:*.tgz=01;31:*.arc=01;31:*.arj=01;31:*.taz=01;31:*.lha=01;31:*.lz4=01;31:*.lzh=01;31:
*.lzma=01;31:*.tlz=01;31:*.txz=01;31:*.tzo=01;31:*.t7z=01;31:*.zip=01;31:*.z=01;31:*.Z=01;31
:*.dz=01;31:*.gz=01;31:*.lrz=01;31:*.lz=01;31:*.lzo=01;31:*.xz=01;31:*.bz2=01;31:*.bz=01;31:
*.tbz=01;31:*.tbz2=01;31:*.tz=01;31:*.deb=01;31:*.rpm=01;31:*.jar=01;31:*.war=01;31:*.ear=01
;31:*.sar=01;31:*.rar=01;31:*.alz=01;31:*.ace=01;31:*.zoo=01;31:*.cpio=01;31:*.7z=01;31:*.rz
=01;31:*.cab=01;31:*.jpg=01;35:*.jpeg=01;35:*.gif=01;35:*.bmp=01;35:*.pbm=01;35:*.pgm=01;35:
*.ppm=01;35:*.tga=01;35:*.xbm=01;35:*.xpm=01;35:*.tif=01;35:*.tiff=01;35:*.png=01;35:*.svg=0
1;35:*.svgz=01;35:*.mng=01;35:*.pcx=01;35:*.mov=01;35:*.mpg=01;35:*.mpeg=01;35:*.m2v=01;35:*
.mkv=01;35:*.webm=01;35:*.ogm=01;35:*.mp4=01;35:*.m4v=01;35:*.mp4v=01;35:*.vob=01;35:*.qt=01
;35:*.nuv=01;35:*.wmv=01;35:*.asf=01;35:*.rm=01;35:*.rmvb=01;35:*.flc=01;35:*.avi=01;35:*.fl
i=01;35:*.flv=01;35:*.gl=01;35:*.dl=01;35:*.xcf=01;35:*.xwd=01;35:*.yuv=01;35:*.cgm=01;35:*.
emf=01;35:*.axv=01;35:*.anx=01;35:*.ogv=01;35:*.ogx=01;35:*.aac=01;36:*.au=01;36:*.flac=01;3
6:*.mid=01;36:*.midi=01;36:*.mka=01;36:*.mp3=01;36:*.mpc=01;36:*.ogg=01;36:*.ra=01;36:*.wav=
01;36:*.axa=01;36:*.oga=01;36:*.spx=01;36:*.xspf=01;36:
MAIL=/var/spool/mail/root
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/root/bin
PWD=/root
LANG=C
SHLVL=1
HOME=/root
linux=6.5
LOGNAME=root
SDKMAN_DIR=/root/.sdkman
SSH_CONNECTION=182.150.21.5 10175 10.104.200.13 22
LESSOPEN=||/usr/bin/lesspipe.sh %s
PROMPT_COMMAND=history -a
XDG_RUNTIME_DIR=/run/user/0
HISTTIMEFORMAT=%F %T 
_=/usr/bin/env
```
$PWD：pwd命令就是调用了这个变量才能进行输出
```
[root@VM_200_13_centos ~]# echo $PWD
/root
[root@VM_200_13_centos ~]# pwd
/root
[root@VM_200_13_centos ~]# cd /usr/local/
[root@VM_200_13_centos local]# echo $PWD
/usr/local
[root@VM_200_13_centos local]# pwd
/usr/local
```
$PATH：定义命令的默认搜索路径，mysql安装后将可执行程序命令路径写入到这个变量中才能在系统任意位置使用mysql命令
```
[root@VM_200_13_centos local]# echo $PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/root/bin
```
$USER
```
[root@VM_200_13_centos local]# echo $USER
root
[root@VM_200_13_centos local]# useradd test
[root@VM_200_13_centos local]# su - test
Last failed login: Wed Aug 24 12:43:20 CST 2016 from 86.247.205.69 on ssh:notty
There were 8 failed login attempts since the last successful login.
[test@VM_200_13_centos ~]$ echo $USER
test
```
$SHELL
```
[root@VM_200_13_centos ~]# echo $SHELL
/bin/bash
```
$HOME
```
[root@VM_200_13_centos ~]# echo $HOME
/root
```
编写脚本放到$PATH的默认搜索路径中去
```
[root@VM_200_13_centos ~]# cat test.sh 
#!/bin/bash
echo "This is an test shell script"
echo "It will output two lines message on srcreen and do some thing"
a=100
b=33
c=$(expr $b % $a)
echo $c
[root@VM_200_13_centos ~]# pwd
/root
[root@VM_200_13_centos ~]# PATH="$PATH:/root"
[root@VM_200_13_centos ~]# source /etc/profile
[root@VM_200_13_centos ~]# ll test.sh 
-rw-r--r-- 1 root root 154 Aug 31 16:56 test.sh
[root@VM_200_13_centos ~]# chmod +x test.sh 
[root@VM_200_13_centos ~]# ll test.sh 
-rwxr-xr-x 1 root root 154 Aug 31 16:56 test.sh
[root@VM_200_13_centos ~]# test.sh
This is an test shell script
It will output two lines message on srcreen and do some thing
33
```
## 位置变量
当执行命令行操作时第一个字段表示命令字或程序名  
其余的字符串参数按照从左到右的顺序依次给位置变量赋值  
位置变量也成为位置参数，使用$1 $2 $3 …$9表示  
```
[root@VM_200_13_centos ~]# cat postion.sh 
#!/bin/bash
postion1=$1
psotion2=$2
result=$(expr $1 + $2)
echo $result
[root@VM_200_13_centos ~]# . postion.sh 33 50
83
```
## 预定义变量
$#：命令行中位置变量的个数（程序执行了几个位置参数）  
$*：所有位置变量的内容（具体的内容，比如/boot就是一个具体的内容）  
$?：上一条命令执行后返回的状态，0时表示执行正常，非0值表示执行异常或出错 ，判断是否出现错误正常为0异常错误为非0，取值在1-127之间  
$0：当前执行的进程/程序名(就是当前执行的命令或程序的名字)  
```
[root@VM_200_13_centos ~]# cat backup.sh
#!/bin/bash
current_time=$(date +%s)
file="backup"$current_time".tar.gz"
log="backup"$current_time".log"
tar -zcf $file $* &> $log
echo "process $0"
echo "backup $# files"
echo "backup package include: $*"
echo "process code: $?"
cat $log
[root@VM_200_13_centos ~]# ./backup.sh  /home /opt
process ./backup.sh
backup 2 files
backup package include: /home /opt
process code: 0
tar: Removing leading `/' from member names
[root@VM_200_13_centos ~]# ls
backup.sh  backup1472636597.log  backup1472636597.tar.gz
```
## 文件测试
shell中测试文件或目录是否存在以及是否有相关权限有以下两种方式

1. test 条件表达式  
2. [ 条件表达式 ]  

| 常用选项 | 说明 |
| :------------- | :------------- |
| -d | 测试是否为目录（Directory）或目录是否存在 |
| -e | 测试目录或文件是否存在（Exist） |
| -f | 测试是否为文件（File）或文件是否存在 |
| -r | 测试当前用户是否有权限读取（Read） |
| -w | 测试当前用户是否有权限写入（Write） |
| -x | 测试当前用户是否有权限执行（eXcute） |

文件测试表达式的结果使用$?进行确认是否存在
```
[root@VM_200_13_centos ~]# [ -d /home ]
[root@VM_200_13_centos ~]# echo $?
0
[root@VM_200_13_centos ~]# [ -d /ex ]
[root@VM_200_13_centos ~]# echo $?
1
[root@VM_200_13_centos ~]# test --help
[root@VM_200_13_centos ~]# echo $?
0
[root@VM_200_13_centos ~]# [ -d /home ]
[root@VM_200_13_centos ~]# echo $?
0
[root@VM_200_13_centos ~]# [ -d /ex ]
[root@VM_200_13_centos ~]# echo $?
1
[root@VM_200_13_centos ~]# test -d /root
[root@VM_200_13_centos ~]# echo $?
0
[root@VM_200_13_centos ~]# test -f /root
[root@VM_200_13_centos ~]# echo $?
1
[root@VM_200_13_centos ~]# touch new.txt
[root@VM_200_13_centos ~]# [ -f new.txt ] && echo "file is exist"
file is exist
[root@VM_200_13_centos ~]# ll new.txt                            
-rw-r--r-- 1 root root 0 Aug 31 17:53 new.txt
[root@VM_200_13_centos ~]# [ -x new.txt ] && echo "has excute permission"
[root@VM_200_13_centos ~]# chmod +x new.txt                              
[root@VM_200_13_centos ~]# [ -x new.txt ] && echo "has excute permission"
has excute permission
[root@VM_200_13_centos ~]# test -x new.txt && echo "has excute permission"
has excute permission
```

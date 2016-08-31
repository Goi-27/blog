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
| ------------- | ------------- |
| 使用绝对路径执行 | 需要执行权限 |
| 使用相对路径执行 | 需要执行权限 |
| sh命令执行 | 不需要执行权限<br>使用-x选项还会输出执行过程 |
| . 脚本名 | 不需要执行权限|
| source 脚本名 | 不需要执行权限<br>主要用于生效配置文件 |
查看example.sh脚本的执行权限
```shell
[root@VM_200_13_centos ~]# ll example.sh 
-rw-r--r-- 1 root root 130 Aug 31 10:09 example.sh
```
授予其执行权限
```shell
[root@VM_200_13_centos ~]# ll example.sh 
-rwxr-xr-x 1 root root 130 Aug 31 10:09 example.sh
```
执行
```shell
[root@VM_200_13_centos ~]# ./example.sh 
this is an example for shell script
system will print two line messages on screen
```
## 变量与特殊变量应用
脚本中最重要的东西就是变量  
变量的定义是：可以存放一个可变的值的空间  
可以通过不同的环境进行改变就是一个可以变的值  
**默认情况下: 在Linux中可以将每个shell看成不同的执行环境,所以相同的一个变量名称在不同的变量执行环境中的变量值是不同的**   
常见的shell变量分类：自定义变量、环境变量、位置变量、预定义变量  
变量的输出语法：echo $变量名
### 自定义变量
>自定义变量是用户根据自己的环境自己定义的变量，Bash中比较简单的变量  
不用进行提前声明，而是直接指定变量名称并赋给初始值  

定义变量的基本格式为：变量名=变量值
```shell
[root@VM_200_13_centos ~]# user=sakuraph
```
### 变量的引用
```shell
[root@VM_200_13_centos ~]# echo $user
sakuraph
```

# 局部变量与全局变量
# 条件测试表达式
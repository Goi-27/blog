>本文介绍RHEL7下which、whereis、locate、find命令的使用，重点介绍find命令的使用

## which
命令：which  
作用：查找命令的执行文件路径  
语法：which [选项] [--] 名称...  
说明：which命令比较简单，他的选项都是不常用的
```
[root@localhost ~]# which -- nginx
/usr/bin/which: no nginx in (/usr/lib64/qt-3.3/bin:/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/
sbin:/usr/bin:/usr/local/java/jdk1.7.0_79/bin:/usr/local/mysql/bin:/root/bin)
[root@localhost ~]# which nginx cat passwd
/usr/bin/which: no nginx in (/usr/lib64/qt-3.3/bin:/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/
sbin:/usr/bin:/usr/local/java/jdk1.7.0_79/bin:/usr/local/mysql/bin:/root/bin)
/bin/cat
/usr/bin/passwd
```
## whereis
命令：whereis  
作用：定位可执行文件、源代码文件、帮助文件在文件系统中的位置  
语法：whereis [-bmsu] [BMS 目录名 -f ] 文件名  

| 选项 | 说明 |
| :------------- | :------------- |
| -b | 定位可执行文件 |
| -m | 定位帮助文件 |
| -s | 定位源代码文件 |
| -u | 搜索默认路径下除可执行文件、源代码文件、帮助文件以外的其它文件 |
| -B | 指定搜索可执行文件的路径 |
| -M | 指定搜索帮助文件的路径 |
| -S | 指定搜索源代码文件的路径 |
| -f | -M-S或-B选项查找的终止信号 |
```
[root@localhost ~]# whereis tomcat
tomcat: /usr/local/tomcat
```
## locate
>locate让使用者可以很快速的搜寻档案系统内是否有指定的档案  
其方法是先建立一个包括系统内所有档案名称及路径的数据库  
之后当寻找时就只需查询这个数据库，而不必实际深入档案系统之中了

命令：locate  
作用：通过索引数据库查找文件  
语法：locate [选项]... [PATTERN]...  

| 选项 | 说明 |
| :------------- | :------------- |
| -b | 只匹配路径名的基本名称 |
| -c | 只打印查询结果总数 |
| -e | 只查询已经存在的文件 |
| -L | 搜寻符号链接文件时追踪到链接的目标文件 |
| -i | 忽略大小写查找 |
| -l | 限制查询输出的结果数，需要跟上数量 |
| -P | 不追踪链接文件，与-L相反 |
| -0 | 不分隔显示输出的结果 |
| -S | 打印数据库中统计的信息，此选项不能和其他选项一起使用 |
| -q | 报告没有读取数据库的错误消息  |
| -r | 使用正则表达式模式进行匹配 |
| -w | 匹配整个路径搜寻（默认使用该选项）|
```
[root@localhost ~]# locate /etc/passwd
/etc/passwd
/etc/passwd-
```
**如果使用locate查询不到，则使用命令updatedb更新下mlocate数据库即可**
```
[root@localhost ~]# touch abcd123456dcba.txt
[root@localhost ~]# locate abcd123456dcba.txt
[root@localhost ~]# updatedb
[root@localhost ~]# locate abcd123456dcba.txt
/root/abcd123456dcba.txt
```
## find
>find命令是在目录结构中搜索文件，并执行指定的操作  
find命令提供了相当多的查找条件，功能很强大

命令：find  
作用：
语法：find [选项] [路径...] [表达式]
说明：
+ 默认的路径是当前目录
+ 默认的表达式为-print
+ 表达式包括： operators, options, tests, actions  

**operators即逻辑运算符，默认为and,优先级顺序为：**  
(表达式)、!表达式、-not 表达式、表达式1 -a 表达式2、  
表达式1 -and 表达式2、表达式1 -o 表达式2、表达式1 -or 表达式2、  
表达式1，表达式2  
即优先级：  
括号>!>-not>-a>-and>-o>-or  
多个表达式用逗号隔开前一个优先级高于后一个

**actions即动作，默认为-print，以下是常见的动作：**  
-delete -print0 -printf FORMAT -fprintf FILE FORMAT -print  
-fprint0 FILE -fprint FILE -ls -fls FILE -prune -quit  
-exec COMMAND  
-exec COMMAND {} + -ok COMMAND  
-execdir COMMAND  
-execdir COMMAND {} + -okdir COMMAND

**find命令的表达式非常多下面只介绍常用的几种：**

| 表达式 | 说明 |
| :------------- | :------------- |
| -print | 将匹配的文件输出到标准输出 |
| -name  | 按照文件名查找文件 |
| -perm | 按照文件权限来查找文件 |
| -prune | 使用这一选项可以使find命令不在当前指定的目录中查找，即排除<br>如果同时使用-depth选项，那么-prune将被find命令忽略 |
| -depth | 在查找文件时，首先查找当前目录中的文件<br>然后再在其子目录中查找 |
| -user | 按照文件属主来查找文件 |
| -nouser | 查找无有效属主的文件<br>即该文件的属主在/etc/passwd中不存在 |
| -group | 按照文件所属的组来查找文件 |
| -nogroup | 查找无有效所属组的文件<br>即该文件所属的组在/etc/groups中不存在 |
| -mtime -n/+n  | 按照文件的更改时间来查找文件<br>-n表示文件更改时间距现在n天以内<br>+n表示文件更改时间距现在n天以前 |
| -type | 查找某一类型的文件<br>b 块设备文件<br>d 目录<br>c 字符设备文件<br>p 管道文件<br>l 符号链接文件<br>f 普通文件 |
| -size n [c] | 查找文件长度为n块的文件<br>带有c时表示文件长度以字节计 |
| -fstype | 查找位于某一类型文件系统中的文件<br>这些文件系统类型通常可以在配置文件/etc/fstab中找到<br>该配置文件中包含了本系统中有关文件系统的信息 |
| -mount | 在查找文件时不跨越文件系统mount点 |
| -follow | 如果find命令遇到符号链接文件，就跟踪至链接所指向的文件 |

### 按名称查找
>-name选项是find命令最常用的选项，要么单独使用该选项，要么和其他选项一起使用

**注意：可以使用某种文件名模式来匹配文件，记住要用引号将文件名模式引起来**

不管当前路径是什么，如果想要在自己的根目录$HOME中查找文件名符合*.txt的文件
则使用~作为'pathname'参数，波浪号~代表了你的$HOME目录
```
[root@VM_200_13_centos ~]# touch tokyohot.txt
[root@VM_200_13_centos ~]# find ~ -name "*.txt"
/root/tokyohot.txt
[root@VM_200_13_centos ~]# cd /
[root@VM_200_13_centos /]# find ~ -name "*.txt"
/root/tokyohot.txt
```
在当前目录及子目录中查找所有的*.txt文件
```
[root@VM_200_13_centos firmware]# find . -name "*.txt" -print
./ar3k/1020201/RamPatch.txt
./ar3k/30101/RamPatch.txt
./ar3k/30101coex/RamPatch.txt
./ar3k/1020200/RamPatch.txt
./ar3k/30000/RamPatch.txt
./qca/NOTICE.txt
./ath10k/QCA988X/hw2.0/notice_ath10k_firmware-4.txt
./TDA7706_OM_v3.0.2_boot.txt
./TDA7706_OM_v2.5.1_boot.txt
```
想要在/etc目录中查找文件名以host开头的文件
```
[root@VM_200_13_centos firmware]# find /etc -name "host*"    
/etc/selinux/targeted/modules/active/modules/hostname.pp
/etc/host.conf
/etc/hosts
/etc/hosts.deny
/etc/hostname
/etc/hosts.allow
```
### 按权限查找
>按文件权限模式来查找文件的话。最好使用三位十进制的权限表示法

**如在当前目录下查找文件权限位为755的文件，即属主可以读、写、执行，其他用户可以读、执行的文件**
```
[root@VM_200_13_centos ~]# find -perm 755       
[root@VM_200_13_centos ~]# ll
total 0
-rw-r--r-- 1 root root 0 Aug 17 00:47 tokyohot.txt
[root@VM_200_13_centos ~]# chmod 755 tokyohot.txt
[root@VM_200_13_centos ~]# find -perm 755
./tokyohot.txt
```
### 忽略某个目录
>使用-prune选项来指出需要忽略的目录

**在使用该选项时如果同时使用-depth选项，那么-prune选项就会被find命令忽略**  
/apps目录下查找文件，但不希望在/apps/bin目录下查找
```
[root@VM_200_13_centos ~]# find /apps -path "/apps/bin" -prune -o -print
/apps
/apps/d
/apps/c
```
### 查找子目录
>可能希望先匹配当前所有的文件，再在子目录中查找  
在下面的例子中， find命令从文件系统的根目录开始，查找一个名为CON.FILE的文件  
它将首先匹配所有的文件然后再进入子目录中查找

```
[root@VM_200_13_centos ~]# find / -depth -name "CON.FILE"  -print
/CON.FILE
```
### 按属主查找
>按文件属主查找文件，如在$HOME目录中查找文件属主为root的文件

```
[root@VM_200_13_centos ~]# find -user root  -print
.
./a
./.bash_history
./.ssh
./.ssh/authorized_keys
./tokyohot.txt
./.bashrc
./.cshrc
./.bash_logout
./.tcshrc
./.bash_profile
```
为了查找属主帐户已经被删除的文件，可以使用-nouser选项  
这样就能够找到那些属主在/etc/passwd文件中没有有效帐户的文件  
在使用-nouser选项时，不必给出用户名； find命令能够为你完成相应的工作
```
root@VM_200_13_centos ~]# find /etc/ -nouser
```
### 按属组查找
>就像user和nouser选项一样，针对文件所属于的用户组， find命令也具有同样的选项

```
[root@VM_200_13_centos ~]# find -group root -print
.
./a
./.bash_history
./.ssh
./.ssh/authorized_keys
./tokyohot.txt
./.bashrc
./.cshrc
./.bash_logout
./.tcshrc
./.bash_profile
./.history
./.viminfo
```
要查找没有有效所属用户组的所有文件，可以使用nogroup选项
```
[root@VM_200_13_centos ~]# groupadd xxx
[root@VM_200_13_centos ~]# touch xx.txt
[root@VM_200_13_centos ~]# chown :xxx xx.txt
[root@VM_200_13_centos ~]# ll xx.txt
-rw-r--r-- 1 root xxx 0 Aug 17 01:21 xx.txt
[root@VM_200_13_centos ~]# find -group xxx
./xx.txt
[root@VM_200_13_centos ~]# groupdel xxx
[root@VM_200_13_centos ~]# find -nogroup    
./xx.txt
```
### 按时间查找
>如果希望按照更改时间来查找文件，可以使用mtime,atime或ctime选项  

**如果系统突然没有可用空间了，很有可能某一个文件的长度在此期间增长迅速，这时就可以用mtime选项来查找这样的文件**  
**用减号-来限定更改时间在距今n日以内的文件**  
**用加号+来限定更改时间在距今n日以前的文件**

希望在系统/root目录下查找更改时间在5日以内的文件
```
[root@VM_200_13_centos ~]# find /root -mtime -5 -print
/root
/root/a
/root/.bash_history
/root/xx.txt
/root/tokyohot.txt
/root/.history
/root/.viminfo
```
### 按类型查找
>-type 查找某一类型的文件

| 类型 | 说明 |
| :------------- | :------------- |
| -type b | 查找块设备 |
| -type d | 查找目录 |
| -type c | 查找字符设备文件 |
| -type p | 查找管道文件 |
| -type l | 查找符号链接文件 |
| -type f | 查找普通文件 |

在/etc目录下查找所有的目录
```
[root@VM_200_13_centos ~]# find /etc -type d
/etc
/etc/selinux
/etc/selinux/targeted
/etc/selinux/targeted/contexts
/etc/selinux/targeted/contexts/users
...
```
### 按文件大小查找
>可以按照文件长度来查找文件  
这里所指的文件长度既可以用块（block）来计量  
也可以用字节来计量  
以字节计量文件长度的表达形式为nc,n代表大小  
以块计量文件长度只用数字表示即可

**在按照文件长度查找文件时，一般使用这种以字节表示的文件长度，在查看文件系统的大小，因为这时使用块来计量更容易转换**

在当前目录下查找文件长度大于1 M字节的文件
```
[root@VM_200_13_centos ~]# find . -size +1000000c -print
```
在/home/apache目录下查找文件长度恰好为100字节的文件
```
[root@VM_200_13_centos ~]# find /home/apache -size 100c -print
```
在当前目录下查找长度超过10块的文件（一块等于512字节）
```
[root@VM_200_13_centos ~]# find . -size +10 -print
```


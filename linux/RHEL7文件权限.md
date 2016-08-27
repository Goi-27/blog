> 本文介绍Linux下的文件权限 操作系统为RHEL7.2_X86_64  
> 可以从以下三种访问方式限制访问权限：  
> 1 只允许用户自己访问  
> 2 允许一个预先指定的用户组中的用户访问  
> 3 允许系统中的任何用户访问  
> 文件的所属用户或者root用户可以将这些权限改变为任何他想指定的权限。  
> 一个只有读权限的文件，是禁止进行任何修改的。  
> 只有执行权限的，允许它想一个程序一样执行。  



## 查看权限
> 我们之前已经很多次用到 ls命令了，如你所见，我们用它来列出并显示当前目录下的文件，当然这是在不带任何参数的情况下，它能做的当然不止这么多，现在我们就要用它来查看文件权限。

```
[root@VM_200_13_centos ~]# ls -l
total 8
drwxr-xr-x 2 root root 4096 Aug 14 11:37 auth
-rw-r--r-- 1 root root    7 Aug 14 11:34 auth.txt
```
## 文件类型
![文件类型](http://img.blog.csdn.net/20160814214728227)
## 基本权限
![基本权限](http://img.blog.csdn.net/20160814214756583)
| 基本权限 | 说明 |
| :------------- | :------------- |
| r | 对文件而言，具有读取文件内容的权限<br>对目录来说，具有浏览目录的权限 |
| w | 对文件而言，具有新增、修改文件内容的权限<br>对目录来说，具有删除、移动目录内文件的权限 |
| x | 对文件而言，具有执行文件的权限<br>对目录了来说该用户具有进入目录的权限 |
| - | 表示不具有该项权限 |
**举例说明：**  

| 例子 | 说明  |
| :------------- | :------------- |
| -rwx------ | 文件所有者对文件具有读取、写入和执行的权限 |
| -rwxr--r-- | 文件所有者具有读、写与执行的权限<br>其他用户则具有读取的权限 |
| -rw-rw-r-x | 文件所有者与同组用户具有读写权限<br>其他用户具有读取和执行权限 |
| drwx--x--x | 目录所有者具有浏览目录修改（删除、移动）目录和进入目录的权限<br>同组用户和其他用户具有进入目录的权限 |
| drwx------ | 除了目录所有者具有完整的权限之外<br>其他用户对该目录完全没有任何权限|
## 特殊权限
>其实文件与目录设置不止这些，还有所谓的特殊权限。由于特殊权限会拥有一些“特权”。  
因而用户若无特殊需求，不应该启用这些权限，避免安全方面出现严重漏洞，造成入侵，甚至摧毁系统!

### s
#### SUID
SUID（Set UID）当一个可以执行文件具有SUID权限，用户执行这个程序时，将以这个程序的所有者身份执行。  
在设置s权限时文件属主必须先设置相应的x权限，否则s权限并不能正真生效。  
chmod命令不进行必要的完整性检查，即使不设置x权限就设置s权限，chmod也不会报错，当我们ls -l时看到rwS，大写S说明s权限未生效。
Linux修改密码的passwd便是个设置了SUID的程序，普通用户无读写/etc/shadow文件的权限确可以修改自己的密码。  
可执行的文件搭配这个权限，便能得到特权，任意存取该文件的所有者能使用的全部系统资源。  
请注意具备SUID权限的文件，骇客经常利用这种权限，以SUID配上root帐号拥有者，无声无息地在系统中开扇后门，供日后进出使用
```
[root@VM_200_13_centos ~]# su - test
Last login: Sun Aug 14 19:05:04 CST 2016 on pts/0
[test@VM_200_13_centos ~]$ which passwd
/bin/passwd
[test@VM_200_13_centos ~]$ ll /bin/passwd
-rwsr-xr-x. 1 root root 27832 Jun 10  2014 /bin/passwd
```
该文件的所属用户和所属用户组均为root，显然test用户不具有该执行文件的读写执行权限
但是该文件被赋予了SUID权限，任意执行该执行文件的用户都能以该文件所属用户（root用户）的身份执行该文件
```
[test@VM_200_13_centos ~]$ passwd
Changing password for user test.
Changing password for test.
(current) UNIX password:
New password:
Retype new password:
passwd: all authentication tokens updated successfully.
```
因为具有SUID权限所有普通用户执行该命令能修改密码
#### SGID
设置在文件上面，其效果与SUID相同，只不过将文件所有者换成用户组，该文件就可以任意存取整个用户组所能使用的系统资源。  
**强调: SUID一般用在文件上(脚本) SGID用在目录上比较多**
### t
t权限代表SBIT（Sticky）：只针对目录有效,对文件无效,作用是防止别人删除掉对方的资料

>因为SUID、SGID、Sticky占用x的位置来表示，所以在表示上会有大小写之分。
加入同时开启执行权限和SUID、SGID、Sticky，则权限表示字符是小写的
如果不具有x权限，会以大写显示

```
[root@VM_200_13_centos ~]# mkdir test
[root@VM_200_13_centos ~]# ll
total 4
drwxr-xr-x 2 root root 4096 Aug 14 19:59 test
[root@VM_200_13_centos ~]# chmod u+s test/
[root@VM_200_13_centos ~]# ll
total 4
drwsr-xr-x 2 root root 4096 Aug 14 19:59 test
[root@VM_200_13_centos ~]# chmod u-x test/
[root@VM_200_13_centos ~]# ll
total 4
drwSr-xr-x 2 root root 4096 Aug 14 19:59 test
[root@VM_200_13_centos ~]# chmod g+s test/
[root@VM_200_13_centos ~]# ll
total 4
drwSr-sr-x 2 root root 4096 Aug 14 19:59 test
[root@VM_200_13_centos ~]# chmod g-x test/
[root@VM_200_13_centos ~]# ll
total 4
drwSr-Sr-x 2 root root 4096 Aug 14 19:59 test
[root@VM_200_13_centos ~]# chmod o+t test/
[root@VM_200_13_centos ~]# ll    
total 4
drwSr-Sr-t 2 root root 4096 Aug 14 19:59 test
[root@VM_200_13_centos ~]# chmod o-x test/
[root@VM_200_13_centos ~]# ll
total 4
drwSr-Sr-T 2 root root 4096 Aug 14 19:59 test
```
## 修改权限

### chmod
命令：chmod
作用：修改文件的权限
语法：chmod [选项] MODE 文件...

| 选项 | 说明 |
| :------------- | :------------- |
| -c | 只输出成功修改权限的文件权限更改信息  |
| -f | 忽略大部分的错误信息 |
| -v | 输出详细信息 |
| -R | 递归更改目录和文件的权限 |
**-rwxrwxrwx**   
-rwxrwxrwx的第一个字符表示这是一个普通文件，之后每三个字符分别代表的是用户权限、用户组权限、其他用户权限  
**用户权限用u表示**  
**用户组权限用g表示**  
**其他用户权限用o表示**  
那么chmod命令的MODE第一种方式可以为指定用户或用户组或其他用户授权  
u+x：表示授予用户对该文件的可执行权限  
g+r:表示授予用户组对该文件的可读权限  
o-w:表示取消其他用户对该文件的写权限  
```
[root@VM_200_13_centos ~]# ll
total 4
drwsr-Sr-T 2 root root 4096 Aug 14 19:59 test
[root@VM_200_13_centos ~]# chmod u-x test/
[root@VM_200_13_centos ~]# chmod g+w test/
[root@VM_200_13_centos ~]# chmod o+w test/
[root@VM_200_13_centos ~]# ll
total 4
drwSrwSrwT 2 root root 4096 Aug 14 19:59 test
```
如果要为文件设置多个用户权限（用户权限、用户组用户权限、其他用户权限）这种方式就很麻烦，需要多次设置  
这种情况使用权限的二进制数表达形式设置会非常效率

| 权限 | 二进制  | 十进制 |
| :------------- | :------------- | :------------- |
| r | 100 | 4 |
| w | 10 | 2 |
| x | 1 | 1 |
| s | SUID：100<br>SGID：10 | SUID：4<br>SGID：2 |
| t | 1 | 1 |
| - | 0 | 0 |

对于 **-rwxrwxrwx** 除去第一位表示文件类型外

rwxrwxrwx每三位为一组用二进制表示：111 111 111  
rwxrwxrwx每三位为一组用十进制表示：7 7 7  
**最终我们使用计算出的十进制数为文件进行授权**
```
[root@VM_200_13_centos ~]# ll
total 0
-rw-r--r-- 1 root root 0 Aug 14 20:41 a
[root@VM_200_13_centos ~]# chmod 000 a
[root@VM_200_13_centos ~]# ll
total 0
---------- 1 root root 0 Aug 14 20:41 a
```
| 例子 | 说明     |
| :------------- | :------------- |
| -rwx------ | 二进制：111000000<br>十进制：700<br>权限：所属用户具有读写执行，所属用户组用户和其他用户没有任何权限 |
| -rwx---rwx | 二进制：111000111<br>十进制：707<br>权限：所属用户和其他用户具有读写执行，所属用户组用户没有任何权限 |
| -rwx--xrwx | 二进制：111001111<br>十进制：717<br>权限：所属用户和其他用户具有读写执行，所属用户组用户只有执行权限 |

```
[root@VM_200_13_centos ~]# chmod 711 a
[root@VM_200_13_centos ~]# ll
total 0
-rwx--x--x 1 root root 0 Aug 14 20:41 a
```
如果要授予特殊权限SUID（4）、SGIU（2）、SBIT（1）则要使用4位1进制数表示
```
[root@VM_200_13_centos ~]# ll
total 0
---------- 1 root root 0 Aug 14 20:41 a
[root@VM_200_13_centos ~]# chmod 7000 a
[root@VM_200_13_centos ~]# ll
total 0
---S--S--T 1 root root 0 Aug 14 20:41 a
```
### chown
命令：chown
作用：修改文件拥有者和所属组
语法：chown [选项] [所属用户][:所属用户组] 文件...

| 选项 | 说明 |
| :------------- | :------------- |
| -c | 只输出成功修改权限的文件权限更改信息  |
| -f | 忽略大部分的错误信息 |
| -v | 输出详细信息 |
| -h | 修复符号链接 |
| -R | 递归更改目录和文件的权限 |
| --deference | 作用于符号链接的指向，而不是链接文件本身 |
```
[root@VM_200_13_centos ~]# ll
total 0
---S--S--T 1 root root 0 Aug 14 20:41 a
[root@VM_200_13_centos ~]# chown test:test a
[root@VM_200_13_centos ~]# ll
total 0
------S--T 1 test test 0 Aug 14 20:41 a
[root@VM_200_13_centos ~]# chown root a
[root@VM_200_13_centos ~]# ll
total 0
------S--T 1 root test 0 Aug 14 20:41 a
[root@VM_200_13_centos ~]# chown :test1 a
[root@VM_200_13_centos ~]# ll
total 0
------S--T 1 root test1 0 Aug 14 20:41 a
[root@VM_200_13_centos ~]# mkdir -p ~/aa/b/c
[root@VM_200_13_centos ~]# touch aa/a
[root@VM_200_13_centos ~]# chown -R -v  :test1 aa/
ownership of 'aa/a' retained as root:test1
ownership of 'aa/b/c' retained as root:test1
ownership of 'aa/b' retained as root:test1
ownership of 'aa/' retained as root:test1
```
## 默认权限
我们创建文件的默认权限是怎么来的？如何改变这个默认权限呢？

umask设置了用户创建文件的默认权限，它与chmod的效果刚好相反。 
umask设置的是权限补码，而chmod设置的是文件权限码。  
一般在/etc/profile、宿主目录下的.bash_profile或 .profile中设置了umask的默认值。
![默认的文件权限](http://img.blog.csdn.net/20160814214844287)
umask命令允许你设定文件创建时的缺省模式，对应每一类用户(文件属主、同组用户、其他用户)存在一个相应的umask值中的数字。  
对于文件来说，这一数字的最大值分别是6。  
系统不允许你在创建一个文本文件时就赋予它执行权限，必须在创建后用chmod命令增加这一权限。

目录则允许设置执行权限，这样针对目录来说，umask中各个数字最大可以到7。  
该命令的一般形式为：umask nnn 其中nnn为umask置000 - 777。   
**我们只要记住umask是从权限中拿走相应的位即可。**  
如：umask值为022，则默认目录权限为755，默认文件权限为644。

计算方法：
文件默认权限＝666-umask值   666-022=644
目录默认权限＝777-umask 值   777-022=755
举例:创建 一个文件:
umask=033
touch c.txt
那么c.txt的默认文件权限是?

umask掩码为033
666-033=633  结果为：644
## 文件属性
命令：chattr
作用：可改变存放在ext2、ext3、ext4、xfs、ubifs、reiserfs、jfs等文件系统上的文件或目录属性
语法：chattr [选项] [属性] [-v（版本）] 文件...
| 选项 | 说明 |
| :------------- | :------------- |
| -R | 递归处理，将指定目录下的所有文件及子目录一并处理 |
| -v | <版本编号> 设置文件或目录版本 |
| -V | 显示指令执行过程 |
| + | 开启文件或目录的指定属性 |
| - | 关闭文件或目录的指定属性 |
| = | 指定文件或目录的指定属性 |

| 属性 | 说明 |
| :------------- | :------------- |
| A | 即Atime，告诉系统不要修改对这个文件的最后访问时间 |
| S | 即Sync，一旦应用程序对这个文件执行了写操作<br>使系统立刻把修改的结果写到磁盘 |
| a | 即Append Only，系统只允许在这个文件之后追加数据<br>不允许任何进程覆盖或截断这个文件<br>如果目录具有这个属性，系统将只允许在这个目录下建立和修改文件<br>而不允许删除任何文件 |
| b | 不更新文件或目录的最后存取时间 |
| c | 将文件或目录压缩后存放 |
| d | 当dump程序执行时，该文件或目录不会被dump备份 |
| D | 检查压缩文件中的错误 |
| i | 即Immutable，系统不允许对这个文件进行任何的修改<br>如果目录具有这个属性，那么任何的进程只能修改目录之下的文件<br>不允许建立和删除文件 |
| s | 彻底删除文件，不可恢复，因为是从磁盘上删除<br>然后用0填充文件所在区域 |
| u | 当一个应用程序请求删除这个文件<br>系统会保留其数据块以便以后能够恢复删除这个文件<br>用来防止意外删除文件或目录 |
| t | 文件系统支持尾部合并（tail-merging） |
| X | 可以直接访问压缩文件的内容 |
**注意事项：**  
chattr命令的作用很大，其中一些功能是由Linux内核版本来支持的，不过现在生产绝大部分跑的
linux系统都是2.6以上内核了。

通过chattr命令修改属性能够提高系统的安全性，但是它并不适合所有的目录。
chattr命令不能保护/、/dev、/tmp、/var目录。
lsattr命令是显示chattr命令设置的文件属性。
这两个命令是用来查看和改变文件、目录属性的，与chmod这个命令相比，chmod只是改变文件的读写、执行权限，更底层的属性控制是由chattr来改变的。

**应用举例：**   
**用chattr命令防止关键密码文件被修改**
```
[root@VM_200_13_centos ~]# chattr +i /etc/passwd
[root@VM_200_13_centos ~]# lsattr /etc/passwd
----i----------- /etc/passwd
```
要想修改此文件就要把i属性去掉 
```
[root@VM_200_13_centos ~]# chattr -i /etc/passwd
[root@VM_200_13_centos ~]# lsattr /etc/passwd
---------------- /etc/passwd
```

**让某个文件只能往里面追加数据但不能删除**
适用于各种日志文件，还是以密码文件为例
```
[root@VM_200_13_centos ~]# chattr +a /etc/passwd
[root@VM_200_13_centos ~]# lsattr /etc/passwd
-----a---------- /etc/passwd
[root@VM_200_13_centos ~]# chattr -a /etc/passwd
[root@VM_200_13_centos ~]# lsattr /etc/passwd
---------------- /etc/passwd
```


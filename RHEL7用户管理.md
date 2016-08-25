> 本文介绍Linux的用户管理

## 用户管理
Linux 是一个可以实现多用户登陆的操作系统，不同用户可以同时登陆同一台主机，他们共享一些主机的资源，但他们也分别有自己的用户空间，用于存放各自的文件。  
但实际上他们的文件都是放在同一个物理磁盘上的甚至同一个逻辑分区或者目录里，但是由于用户管理和权限机制，不同用户不可以轻易地查看、修改彼此的文件。
与windows相比LINUX中的用户和账号的作用是一样的。
都是基于用户对访问的资源做控制,只不过在表示的细节上有差异。
## 用户帐号

| 类型   | 说明     |
| :------------- | :------------- |
| 超级用户 | root账户拥有整个系统至高无上的权利 |
| 本地用户 | 由管理员创建,权限受到一定的限制,在自己的宿主目录是完整的权限 |
| 系统用户 | 一般是不会登录系统的,用于维持某个服务程序正常运行 |
| 伪用户 | linux中任何一个命令的操作都必须有一个用户的身份<br>伪用户一般和系统或者程序服务相关<br>bin、daemon、shutdown 、halt等<br>linux默认都有这些伪用户<br>伪用户通常不需要或无法登陆系统，可以没有宿主目录<br>源码安装nginx，运行nginx web服务器默认使用nobody用户|
## 用户分类
**根据帐号的位置：可分为本地帐号、远程（域）帐号**  
**根据帐号的功能：**

| 类型    | UID     |
| :------------- | :------------- |
| 超级用户（root）| 0 |
| 普通用户 | 1-999为系统用户<br>1000+为本地用户<br>RHEL中本地用户UID从500开始 |

>UID即每个用户的身份标示,类似于每个人的身份证号码

## 用户组分类
**根据帐号的位置：可分为本地用户组、远程（域）用户组**  
**根据帐号的功能：**

| 类型    | GID     |
| :------------- | :------------- |
| 超级用户组（root）| 0 |
| 普通用户组 | 1-999为系统用户组<br>1000+为本地用户组 |
## 配置文件
**关于用户和组的相关配置文件存放在以下目录中：**

| 类型 | 账户信息 | 密码信息     |
| :-------------| :------------- | :------------- |
| 用户| /etc/passwd | /etc/shadow |
| 组 | /etc/group | /etc/gshadow |
### 用户账户信息文件说明
**用户的账户信息保存在/etc/passwd文件中**  
**这个文件以如下格式保存了每系统帐户的所有信息：**
```
root:x:0:0:root:/root:/bin/bash
```
用户名:密码占位符:UID:GID:用户描述:宿主目录:登录的shell
### 用户密码信息文件说明
**用户的密码信息保存在/etc/shadow文件中**  
**这个文件以如下格式保存了系统帐户的密码信息：**
```
root:$1$LTbeEPHI$AuZ6b2iHVNdekNHu2WvSP/::0:99999:7:::
```
用户名:加密后的密码:最近更改密码的日期:密码不能修改的天数:密码过期时间:密码需要更改期限到来的前7天发出警告:密码过期了几天后还能修改密码:帐号过期时间:保留
### 用户组信息文件说明
**用户组信息保存在/etc/shadow文件中**  
**这个文件以如下格式保存了系统用户组的所有信息：**
```
root:x:0:tangbb
```
组名:组密码占位符:GID:组成员列表
### 用户组密码信息文件说明
**用户组密码信息保存在/etc/gshadow文件中**  
**这个文件以如下格式保存了系统用户组的密码信息：**
```
root:x:0:
```
组名:组密码:组管理者列表:组用户列表  
>/etc/gshadow和/etc/group是互补的两个文件；对于大型服务器，针对很多用户和组，定制一些关系结构比较复杂的权限模型，设置用户组密码是极有必要的。
比如我们不想让一些非用户组成员永久拥有用户组的权限和特性，这时我们可以通过密码验证的方式来让某些用户临时拥有一些用户组特性，这时就要用到用户组密码。

### 解决模板文件被删之后用户名显示不正常的问题
>当添加新的用户帐户时，默认会执行下列操作：  
 它的主目录就会被创建(一般是"/home/用户名"，除非你特别设置)
一些隐藏文件 如.bash_logout, .bash_profile 以及 .bashrc 会被复制到用户的主目录
会创建一个和用户名同样的组（除非你给新创建的用户指定了组）

+ 删除用户的规则文件
```
[root@VM_200_13_centos ~]# useradd test
[root@VM_200_13_centos ~]# echo 123456 | passwd --stdin test
Changing password for user test.
passwd: all authentication tokens updated successfully.
[root@VM_200_13_centos ~]# ls -a /home/test
.  ..  .bash_logout  .bash_profile  .bashrc
[root@VM_200_13_centos ~]# rm -rf /home/test/.bash*
[root@VM_200_13_centos ~]# su - test
Password:
bash-4.2$ su - test
```
**从上面最后一行看出用户名显示为bash-4.2**  

+ 恢复用户的规则文件
```
bash-4.2$ su - test
Password:
Last login: Sun Aug 14 01:38:21 CST 2016 on pts/0
-bash-4.2$ su
Password:
[root@VM_200_13_centos test]# cp /etc/skel/.bash* /home/test/
[root@VM_200_13_centos test]# chown test:test /home/test/.bash*
[root@VM_200_13_centos test]# su - test
Last login: Sun Aug 14 01:40:15 CST 2016 on pts/0
[test@VM_200_13_centos ~]$
```
**从上面最后一行看出用户名已经正常显示**

## 用户操作
> 介绍用户的添加删除等操作

### 添加用户
命令：useradd  
作用：添加用户  
语法：useradd [选项] 用户名  

| 选项 | 说明 |
| :------------- | :------------- |
| -u | 创建用户时指定UID |
| -d | 创建用户时指定宿主目录<br>不使用该选项时会默认会以用户名在/home目录下创建宿主目录 |
| -g | 创建用户时指定起始组（起始组只能有一个）|
| -G | 创建用户时指定附加组（可以有多个附加组）|
| -s | 创建用户时指定登录的shell |
| -M | 创建用户时不创建宿主目录 |
**创建用户tangpeng**
```
[root@VM_200_13_centos ~]# ls /home
[root@VM_200_13_centos ~]# useradd tangpeng
[root@VM_200_13_centos ~]# ls /home
tangpeng
```
**指定UID创建用户tangdd**
```
[root@VM_200_13_centos ~]# useradd -u 9527 tangdd   
[root@VM_200_13_centos ~]# ls /home
tangdd  tangpeng
```
**创建用户tangjj指定宿主目录为/home/zhazha**
```
[root@VM_200_13_centos ~]# useradd tangjj -d /home/zhazha
[root@VM_200_13_centos ~]# ls /home
tangdd  tangpeng  zhazha
```
**创建用户tangmm指定起始组为tangdd**
```
[root@VM_200_13_centos ~]# useradd -g tangdd tangmm
[root@VM_200_13_centos ~]# id tangmm
uid=9529(tangmm) gid=9528(tangdd) groups=9528(tangdd)
```
**创建用户指定附加组为root**
```
[root@VM_200_13_centos ~]# id tangbb
uid=9530(tangbb) gid=9530(tangbb) groups=9530(tangbb),0(root)
```
**指定用户登录的shell且不创建宿主目录**
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
[root@VM_200_13_centos ~]# useradd -M -s /usr/sbin/nologin nginx
[root@VM_200_13_centos ~]# ls /home
tangbb  tangdd  tangmm  tangpeng  zhazha
```
### 删除用户
命令：userdel  
作用：删除用户  
语法：userdel [选项] 用户名  

| 选项 | 说明 |
| :------------- | :------------- |
| -f | 强制删除用户即便是登录中的用户 |
| -r | 删除宿主目录和邮件 |
| -Z| 删除和这个用户关联的SELinux用户 |

**删除用户tangpeng同时删除宿主目录和邮件**
```
[root@VM_200_13_centos ~]# userdel -r tangpeng
[root@VM_200_13_centos ~]# ls /home
tangbb  tangdd  tangmm  zhazha
```
### 修改用户
命令：usermod  
作用：修改用户  
语法：usermod [选项] 用户名  

| 参数 | 说明 |
| :------------- | :------------- |
| -u | 修改用户UID |
| -d | 修改用户宿主目录 |
| -g | 修改用户起始组 |
| -G | 修改用户附加组 |
| -s | 修改用登录的shell |
### 切换用户
命令：su  
作用：切换用户  
语法：su [选项] [-] 用户名  
说明：加上-会在切换用户时把环境变量一起进行切换  

```
[root@VM_200_13_centos ~]# su - tangdd  
```
### 查看用户
命令：id  
作用：打印指定用户的用户和用户组信息  
语法：id [选项] 用户  

| 选项 | 说明 |
| :------------- | :------------- |
| -Z | 只打印当前用户的安全上下文（只在开启SELinux的内核系统中有效） |
| -g | 只打印有效用户组 |
| -G | 打印所有用户组 |
| -n | 忽略（兼容其他版本） |
| -u | 只打印有效的UID |
命令：w
作用：显示登录到系统的用户
语法：w [选项]

| 选项 | 说明 |
| :------------- | :------------- |
| -h | 不显示有信息 |
| -u | 忽略当前进程用户名  |
| -s | 以短格式输出 |
| -f | 显示远程主机名字段 |
| -o | 使用旧样式数据 |
| -i | 使用ip来替代主机名 |

命令：who
作用：显示当前登录的用户信息
语法：who [选项]... [FILE|ARG1 ARG2]
说明：如果没有指定文件则使用 /var/run/utmp、/var/log/wtmp作为记录文件；ARG1 ARG2可以为am i或者mom likes等同于who -m

| 选项 | 说明 |
| :------------- | :------------- |
| -a | 输出所有信息，等同于 who -b -d -l -p -r -t -T -u |
| -b | 上次系统启动时间 |
| -d | 输出撕掉的进程 |
| -H | 打印行标题栏 |
| -l | 打印登录系统的用户进程 |
| -q | 统计登录的所有用户数量 |
| -r | 输出当前运行级别 |
| -s | 只打印用户名，终端类型时间 |
| -t | 打印上一次系统时间修改 |
| -u | 列出登录恩典用户 |

命令：finger
作用：显示系统用户信息
语法：finger [选项] [用户] [用户@主机]
| 选项 | 说明 |
| :------------- | :------------- |
| -s | 显示用户的注册名、实际用户名、终端名称、写状态、停滞时间、登录时间等信息 |
| -l | 显示用户的注册名、实际用户名、宿主目录、登录shell、登录日志、邮件、计划 |
| -p | 除了不显示.plan文件和.project文件以外，与-l选项相同 |
## 用户组操作
> 介绍用户组的添加删除等操作

### 添加用户组
命令：groupadd  
作用：添加用户组  
语法：groupadd [选项] 组名  

| 选项 | 说明 |
| :------------- | :------------- |
| -f | 如果用户组已经存在则不创建，并取消-g指定的GID |
| -g | 指定GID |
| -p | 指定组密码 |
| -r | 为该组创建系统帐号 |
| -o | 允许使用不唯一的GID创建用户组 |
| -K | 覆写/etc/login.defs文件中创建用户组的默认配置（修改时在该选项后以键值对形式进行修改） |
> GID即用户组的ID

### 删除用户组
命令：groupdel  
作用：删除用户组  
语法：groupdel [选项] 组名  
**该命令比较简单，出去-h选项外的另一个-R选项都是不常用的**
```
[root@VM_200_13_centos ~]# groupdel tangdd
```
### 修改用户组
命令：groupmod  
作用：修改用户组  
语法：groupmod [选项] 组名  

| 选项 | 说明 |
| :------------- | :------------- |
| -g | 修改当前组的GID为指定的GID |
| -n | 修改当前组名为新的组名 |
| -o | 允许使用一个重复的GID |
| -p | 修改组密码 |
## 密码修改
命令：passwd
作用：修改用户密码
语法：passwd [选项] <用户名>
说明：不加用户名则修改当前用户

| 选项 | 说明 |
|:------------- | :------------- |
| -k | 设置只有在密码过期失效后才能修改 |
| -d | 删除密码，只有ROOT用户能使用该选项 |
| -l | 锁定密码，只有ROOT用户能使用该选项 |
| -u | 解除锁定，只有ROOT用户能使用该选项  |
| -e | 使帐号的密码过期，只有ROOT用户能使用该选项 |
| -f | 强制执行 |
| -x | 密码最长使用寿命，只有ROOT用户能使用该选项 |
| -n | 密码最短使用寿命，只有ROOT用户能使用该选项 |
| -w | 在密码过期之前用户收到警告的天数，只有ROOT用户能使用该选项|
| -i | 指定密码过期后的第几天禁用帐号，只有ROOT用户能使用该选项 |
| -S | 报告用户的密码状态，只有ROOT用户能使用该选项 |
| --stdin | 以标准输入设定用户的密码，只有ROOT用户能使用该选项 |

选项都比较简单，最后一个可能比较难懂
```
[root@VM_200_13_centos ~]# echo cnmlgb | passwd --stdin test
Changing password for user test.
passwd: all authentication tokens updated successfully.
```
## 附录  
[RHEL7恢复ROOT密码](http://www.rhce.cc/?p=1201)


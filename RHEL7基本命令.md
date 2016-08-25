## Terminal

### TTY
TTY是TeleTYpe的一个老缩写。  
Teletypes，或者teletypewriters，原来指的是电传打字机，是通过串行线用打印机键盘通过阅读和发送信息的东西，和古老的电报机区别并不是很大。  
之后，当计算机只能以批处理方式运行时（当时穿孔卡片阅读器是唯一一种使程序载入运行的方式），电传打字机成为唯一能够被使用的“实时”输入/输出设备。  
最终，电传打字机被键盘和显示器终端所取代，但在终端或TTY接插的地方，操作系统仍然需要一个程序来监视串行端口。
一个getty“Get TTY”的处理过程是：一个程序监视物理的TTY/终端接口。  
### PTY
但是假如我们远程telnet到主机或使用xterm时不也需要一个终端交互么？是的，这就是虚拟终端pty(pseudo-tty)。
### PTS/PTMX
pts（pseudo-terminal slave）是pty的实现方法，和ptmx（pseudo-terminal master）配合使用实现pty（PTS/PTMX结合使用实现PTY）。
### 实验：验证SSH的终端
在图形界面下打开一个终端,查看当前终端对应的pts号
```
[root@administrator ~]# who am i
root     pts/0        2016-08-07 12:06 (:0)
```
使用SSH连接本机：
```
[root@administrator ~]# ssh root@127.0.0.1
The authenticity of host '127.0.0.1 (127.0.0.1)' can't be established.
ECDSA key fingerprint is 3b:42:8d:2e:84:6f:1e:b9:b6:eb:6d:34:23:b5:f2:57.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '127.0.0.1' (ECDSA) to the list of known hosts.
root@127.0.0.1's password:
Last login: Sun Aug  7 12:02:57 2016 from localhost
```
再次查看pts号：
```
[root@administrator ~]# who am i
root     pts/1        2016-08-07 12:08 (localhost)
```
可以看出已经不是之前的终端号了，说明启用了一个新的伪终端  
查看进程：
```
[root@administrator ~]# ps -aux | grep pts
root       7767  0.0  0.1 116676  3340 pts/0    Ss   12:06   0:00 /bin/bash
root       7820  0.0  0.2  80420  4208 pts/0    S+   12:08   0:00 ssh root@127.0.0.1
root       7821  0.0  0.2 143344  5440 ?        Ss   12:08   0:00 sshd: root@pt/1
root       7824  0.0  0.1 116564  3264 pts/1    Ss   12:08   0:00 -bash
root       7888  0.0  0.0 141576  1672 pts/1    R+   12:11   0:00 ps -aux
root       7889  0.0  0.0 112644   952 pts/1    R+   12:11   0:00 grep --color=auto pts
```
可以看出通过ssh连接后 其实使用的是pts终端  
## Shell提示符
对比普通用户和root用户的Shell提示符  
**普通用户**  
```
[courier@administrator ~]$
```
**root用户**  
```
[root@administrator ~]#
```
[用户名@主机名 当前所在目录]root/普通用户  
$为普通用户 #为root用户  
## Bash Shell 基本语法
使用下面的两个命令查看所有的解释器：
```
[root@administrator root]# cat /etc/shells
/bin/sh
/bin/bash
/sbin/nologin
/usr/bin/sh
/usr/bin/bash
/usr/sbin/nologin
/bin/tcsh
/bin/csh
```
```
[root@administrator root]# chsh -l
/bin/sh
/bin/bash
/sbin/nologin
/usr/bin/sh
/usr/bin/bash
/usr/sbin/nologin
/bin/tcsh
/bin/csh
```
命令的格式：命令字 [选项][参数]  
命令字：具体执行的命令  
选项：匹配的条件  
参数：命令处理的对象  
举例说明：
```
[root@administrator root]# touch example.txt
[root@administrator root]# ls
anaconda-ks.cfg  Documents  example.txt           Music     Public     Videos
Desktop          Downloads  initial-setup-ks.cfg  Pictures  Templates
[root@administrator root]# rm -rf example.txt
[root@administrator root]# ls
anaconda-ks.cfg  Documents  initial-setup-ks.cfg  Pictures  Templates
Desktop          Downloads  Music                 Public    Videos
```
## 常用基本命令
### ls
作用：查看当前目录下有哪些文件  
语法：ls 目录 如果不加目录则查看当前目录  
```
[root@administrator root]# ls
anaconda-ks.cfg  Documents  initial-setup-ks.cfg  Pictures  Templates
Desktop          Downloads  Music                 Public    Videos
[root@administrator ~]# ls ~
anaconda-ks.cfg  Documents  initial-setup-ks.cfg  Pictures  Templates
Desktop          Downloads  Music  
```
**-l 显示详细信息(ll命令等同于ls -l)**
```
[root@administrator ~]# ls -l
total 8
-rw-------. 1 root root 1545 Aug  7 09:04 anaconda-ks.cfg
drwxr-xr-x. 2 root root    6 Aug  7 00:23 Desktop
drwxr-xr-x. 2 root root    6 Aug  7 00:23 Documents
drwxr-xr-x. 2 root root    6 Aug  7 00:23 Downloads
-rw-------. 1 root root 1638 Aug  7 00:23 initial-setup-ks.cfg
drwxr-xr-x. 2 root root    6 Aug  7 00:23 Music
drwxr-xr-x. 2 root root    6 Aug  7 00:23 Pictures
drwxr-xr-x. 2 root root    6 Aug  7 00:23 Public
drwxr-xr-x. 2 root root    6 Aug  7 00:23 Templates
drwxr-xr-x. 2 root root    6 Aug  7 00:23 Videos
```
**-a 显示所有 包括隐藏文件**
```
[root@administrator ~]# ls -a
.                .bashrc    Downloads             Pictures   .viminfo
..               .cache     .esd_auth             Public     .xauthO64wyA
anaconda-ks.cfg  .config    .ICEauthority         .ssh
.bash_history    .cshrc     initial-setup-ks.cfg  .tcshrc
.bash_logout     Desktop    .local                Templates
.bash_profile    Documents  Music                 Videos
```
**-d 查看目录(不查看里面的内容)**
```
[root@administrator ~]# ls -d
.
```
### pwd命令
作用：查看当前所在目录的绝对路径
```
[root@administrator ~]# cd /etc/sysconfig/network-scripts/
[root@administrator network-scripts]# pwd
/etc/sysconfig/network-scripts
```
### cd命令
作用：切换目录  
语法：cd 目录名  
不跟目录名则切换到当前用户目录  
.表示当前目录 ..当前目录的上级目录  
```
[root@administrator network-scripts]# cd
[root@administrator ~]# pwd
/root
[root@administrator ~]# cd /home
[root@administrator home]# pwd
/home
[root@administrator home]# cd /etc/sysconfig/network-scripts/
[root@administrator network-scripts]# cd .
[root@administrator network-scripts]# pwd
/etc/sysconfig/network-scripts
[root@administrator network-scripts]# cd ..
[root@administrator sysconfig]# pwd
/etc/sysconfig
[root@administrator sysconfig]# cd ./../
[root@administrator etc]# pwd
/etc
```
## 系统时间管理
**查看BIOS时间**
```
[root@administrator ~]# hwclock
Sun 07 Aug 2016 01:18:25 PM EDT  -0.147639 seconds
```
**查看系统时间**
```
[root@administrator ~]# date
Sun Aug  7 13:19:29 EDT 2016
```
**修改时间**
```
[root@administrator ~]# date -s 2016-10-1
Sat Oct  1 00:00:00 EDT 2016
```
**格式化时间**
```
[root@administrator ~]# date '+%Y-%m-%d %H:%M'
2016-10-01 00:02
```
## 命令太多如何获取帮助
**命令字 -h/--help**
```
[root@administrator ~]# systemctl --help
[root@administrator ~]# systemctl -h
```
**man 命令字**
```
[root@administrator ~]# man systemctl
```
## 关机命令
### shutdown
作用：关机/重启/定时关机  
语法：shutdown [选项]  
**-r 重启计算机**
```
[root@administrator ~]# shutdown -r
Shutdown scheduled for Mon 2016-08-08 01:38:34 EDT, use 'shutdown -c' to cancel.
[root@administrator ~]#
Broadcast message from root@administrator (Mon 2016-08-08 01:37:34 EDT):

The system is going down for reboot at Mon 2016-08-08 01:38:34 EDT!
```
```
[root@administrator ~]# shutdown -now
```
```
[root@administrator ~]# shutdown -r +10
Shutdown scheduled for Sun 2016-08-07 13:50:56 EDT, use 'shutdown -c' to cancel.
[root@administrator ~]#
Broadcast message from root@administrator (Sun 2016-08-07 13:40:56 EDT):

The system is going down for reboot at Sun 2016-08-07 13:50:56 EDT!
```
```
[root@administrator ~]# shutdown -r 01:50
Shutdown scheduled for Mon 2016-08-08 01:50:00 EDT, use 'shutdown -c' to cancel.
```
**-h 关机**
```
[root@administrator ~]# shutdown -h +10
Shutdown scheduled for Sun 2016-08-07 13:54:14 EDT, use 'shutdown -c' to cancel.

Broadcast message from root@administrator (Sun 2016-08-07 13:44:14 EDT):

The system is going down for power-off at Sun 2016-08-07 13:54:14 EDT!
```
```
[root@administrator ~]# shutdown -h 01:55
Shutdown scheduled for Mon 2016-08-08 01:55:00 EDT, use 'shutdown -c' to cancel.
```
```
[root@administrator ~]# shutdown -h now
```
### init 0 关机
```
[root@administrator ~]# init 0
```
### reboot 重启
```
[root@administrator ~]# reboot
```
### poweroff 关机
```
[root@administrator ~]# poweroff
```
## 启动级别
命令：init  
作用：切换系统运行级别  
语法：init 0-6
### 7个启动级别
0 **系统停机模式** 系统默认运行级别不能设置为0，否则不能正常启动机器关闭  
1 **单用户模式** root权限，用于系统维护，禁止远程登陆，类似windows安全模式  
2 **多用户模式** 没有NFS网络支持  
3 **完整的多用户文本模式** 有NFS网络支持，登陆后进入控制台命令行模式  
4 **系统未使用** 保留一般不用，在一些特殊情况下可以用它来做一些事情，例如：笔记本电脑的电池用尽时可以切换到该模式做一些设置  
5 **图形化模式** 登陆后进入图形GUI模式  
6 **重启模式** 默认运行级别不能设为6，否则不能正常启动，执行init 6机器就会重启
### 启动级别设置
**RHEL7不再使用/etc/inittab文件进行默认的级别配置**  
查看inittab文件
```
[root@administrator ~]# vim /etc/inittab
```
```
# inittab is no longer used when using systemd.
#
# ADDING CONFIGURATION HERE WILL HAVE NO EFFECT ON YOUR SYSTEM.
#
# Ctrl-Alt-Delete is handled by /usr/lib/systemd/system/ctrl-alt-del.target
#
# systemd uses 'targets' instead of runlevels. By default, there are two main targets:
#
# multi-user.target: analogous to runlevel 3
# graphical.target: analogous to runlevel 5
#
# To view current default target, run:
# systemctl get-default
#
# To set a default target, run:
# systemctl set-default TARGET.target
#                 
```
该文件的注释中已经说明不再使用inittab进行默认级别的配置  
根据该文件注释，下面使用红帽7提供的级别进行配置  
**切换到第3运行级**
```
[root@administrator ~]# systemctl isolate multi-user.target
```
```
[root@administrator ~]# systemctl isolate runlevel3.target
```
**切换到第5运行级**
```
[root@administrator ~]# systemctl isolate graphical.target
```
```
[root@administrator ~]# systemctl isolate runlevel5.target
```
**设置默认的运行级别为第三运行级别**
```
[root@administrator ~]# systemctl set-default multi-user.target
Removed symlink /etc/systemd/system/default.target.
Created symlink from /etc/systemd/system/default.target to /usr/lib/systemd/system/multi-user.target.
```
**设置默认的运行级别为第五运行级别**
```
[root@administrator ~]# systemctl set-default graphical.target
Removed symlink /etc/systemd/system/default.target.
Created symlink from /etc/systemd/system/default.target to /usr/lib/systemd/system/graphical.target.
```
**查看当前运行级别**
```
[root@administrator ~]# runlevel
3 5
```
>3为上次的运行级别 5为本次 如果系统刚启动 上次则会显示为N

**查看默认运行级别**
```
[root@administrator ~]# systemctl get-default
graphical.target
```
## 设置服务器在来电后自动开机
>该功能需要BIOS支持

1. 进入BIOS（一般是在开机后出现主板画面时Del键，部分品牌机可能按F2/F1）
2. 找到Power Management Setup进入电源设置
3. 找到Wake Up Event Setup
4. 将Disabledd更改为Enabled
5. 再继续设置时间和日期
6. 保存并退出

## 附录
Linux下不同的颜色代表了不同的文件类型

| 颜色    | 类型     | 举例 |
| :------------- | :------------- | :------------- |
| <font color="Blue">蓝色</font> | 目录 | <font color="Blue">/etc</font> |
| <font color="Black">黑色</font> | 文件 | <font color="Black">/etc/passwd </font> |
| <font color="Cyan">浅蓝色</font> | 链接 | <font color="Cyan">/etc/grub2.cfg</font> |
| <font color="Red">红色</font> | 压缩包 | <font color="Red">boot.tar.gz</font> |
| <font color="Green">绿色</font> | 可执行文件 | <font color="Green">/etc/init.d/network</font> |
| <font color="Chocolate" style="background:black">黑底黄字</font> | 设备文件 | <font color="Chocolate" style="background:black">/dev/sda</font> |
[who、whoami命令 和 who am i 命令的区别](http://www.cnblogs.com/aqxin/archive/2011/05/20/2052075.html "who、whoami命令 和 who am i 命令的区别")  
[Linux中tty、pty、pts的概念区别](http://www.cnblogs.com/zengkefu/p/5558851.html "Linux中tty、pty、pts的概念区别")


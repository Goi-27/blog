## 进程概念
| 名称 | 说明 |
| :------------- | :------------- |
| 程序 | 一组指令的集合 |
| 进程 |程序的执行就是进程也可以把进程看成一个独立的程序<br>在内存中有其对应的代码空间和数据空间，一个进程所拥有的数据和代码只属于自己<br>进程是资源分配的基本单位，也是调度运行的基本单位 |
| 线程 | 线程被人们认为是轻量级的进程，它是进程中单独运行的程序 |
**一个进程可以包含多个线程且至少有一个主线程，同一进程的线程共享该进程的代码和数据**

![程序进程线程](http://img.blog.csdn.net/20160817110609696)
### 程序和进程的区别
+ 程序是**静态**的，它只是一组指令的集合，不具有任何的运行意义。而进程是程序运行的**动态**过程
+ 进程和程序并不是一一对应的关系，相同的程序运行在不同的数据集上就是不同的进程
+ 进程还具有并发性和交往性，而程序却是封闭的

### 进程和线程的区别
+ 一个进程可以拥有多个线程，而一个线程同时只能被一个进程所拥有
+ 线程不能单独执行，但是每一个线程都有程序的入口、执行序列以及程序出口，它必须组成进程才能被执行

### 进程属性
![进程属性](http://img.blog.csdn.net/20160817135642565)
## 进程管理工具
> 对于Linux进程的管理，是通过进程管理工具实现的，比如ps、kill

### 静态查看进程
命令：ps  
作用：查看系统进程（ **静态查看** ）  
语法：ps [选项]

| 选项 | 说明 |
| :------------- | :------------- |
| -a | 显示所有用户的进程 |
| -u | 显示用户名和启动时间 |
| -x | 显示没有控制终端的进程 |
| -e | 显示所有进程，包括没有控制终端的进程 |
| -l | 长格式显示 |
| -w | 宽行显示，可以使用多个w进行加宽显示 |
**我们常用的选项是组合是aux**
查看隶属于自己的进程
```
[root@VM_200_13_centos ~]# ps -u
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root      1275  0.0  0.0   6452   804 ttyS0    Ss+  Aug15   0:00 /sbin/agetty --keep-baud 115200 3
root      1276  0.0  0.0 110032   856 tty1     Ss+  Aug15   0:00 /sbin/agetty --noclear tty1 linux
root      2828  0.0  0.2  12684  2888 pts/0    Ss   14:33   0:00 -bash
root      2979  0.0  0.1  35888  1476 pts/0    R+   14:34   0:00 ps -u
```
**查看所有用户的进程和没有控制终端的进程**
```
[root@VM_200_13_centos ~]# ps -aux
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root         1  0.0  0.7  44800  7272 ?        Ss   Aug15   0:22 /usr/lib/systemd/systemd --switch
root         2  0.0  0.0      0     0 ?        S    Aug15   0:00 [kthreadd]
root         3  0.0  0.0      0     0 ?        S    Aug15   0:00 [ksoftirqd/0]
root         5  0.0  0.0      0     0 ?        S<   Aug15   0:00 [kworker/0:0H]
root         7  0.0  0.0      0     0 ?        S    Aug15   0:00 [migration/0]
root         8  0.0  0.0      0     0 ?        S    Aug15   0:00 [rcu_bh]
root         9  0.0  0.0      0     0 ?        S    Aug15   0:00 [rcuob/0]
root        10  0.0  0.0      0     0 ?        R    Aug15   0:03 [rcu_sched]
root        11  0.0  0.0      0     0 ?        S    Aug15   0:06 [rcuos/0]
root        12  0.0  0.0      0     0 ?        S    Aug15   0:00 [watchdog/0]
root        13  0.0  0.0      0     0 ?        S<   Aug15   0:00 [khelper]
...
```
**ps -aux输出解释：**

| 名称 | 说明 |
| :------------- | :------------- |
| USER | 进程所有者 |
| PID | 进程ID |
| %CPU | 占用的CPU使用率 |
| %MEM | 占用的内存使用率 |
| VSZ | 占用的虚拟内存大小 |
| RSS | 占用的内存大小 |
| TTY | 终端的次要装置号码 (minor device number of tty) |
| STAT | 进程状态 |
| START | 启动进程的时间 |
| TIME | 进程消耗CPU的时间 |
| COMMAND | 命令的名称和参数 |
**进程状态：**

| 状态 | 说明 |
| :------------- | :------------- |
| D | 无法中断的休眠状态（通常为IO进程） |
| R | 正在运行，在可中断队列中 |
| S | 处于休眠状态，静止状态 |
| T | 停止或被追踪，暂停执行 |
| W | 进入内存交换（从内核2.6开始无效） |
| X | 死掉的进程 |
| Z | 僵尸进程不存在但暂时无法消除 |
| < | 高优先级进程 |
| N | 低优先级进程 |
| L | 有记忆体分页分配并锁在记忆体内 |
| s | 进程的领导者（在它之下有子进程） |
| l | 多进程的 |
| + | 位于后台的进程组 |
**查看nginx进程**
```
[root@VM_200_13_centos ~]# ps -aux | grep nginx
root      5092  0.0  0.0   9036   672 pts/0    R+   15:07   0:00 grep --color=auto nginx
root     17698  0.0  0.0  20468   604 ?        Ss   Aug15   0:00 nginx: master process nginx
nginx    17699  0.0  0.1  22980  1612 ?        S    Aug15   0:00 nginx: worker process
```
### 动态查看进程
命令：top  
作用：**动态** 显示当前系统正在执行的进程的相关信息，包括进程ID、内存占用率、CPU占用率等  
语法：top [选项]
说明：默认3s刷新一次，按空格立即刷新，按q退出，按大写M则根据内存排序，按P则根据CPU排序

| 选项 | 说明 |
| :------------- | :------------- |
| -b | 批处理 |
| -c | 显示完整的治命令 |
| -I | 忽略失效过程 |
| -s | 保密模式 |
| -S | 累积模式 |
| -i<时间> | 设置间隔时间 |
| -u<用户名> | 指定用户名 |
| -p<进程号> | 指定进程 |
| -n<次数> | 循环显示的次数 |
```
top - 15:58:43 up 2 days, 14:09,  1 user,  load average: 0.00, 0.01, 0.05
Tasks:  77 total,   3 running,  74 sleeping,   0 stopped,   0 zombie
%Cpu(s):  0.2 us,  0.2 sy,  0.0 ni, 99.5 id,  0.1 wa,  0.0 hi,  0.0 si,  0.0 st
KiB Mem :  1016916 total,   533700 free,    90484 used,   392732 buff/cache
KiB Swap:        0 total,        0 free,        0 used.   768064 avail Mem

  PID USER      PR  NI    VIRT    RES    SHR S %CPU %MEM     TIME+ COMMAND                       
    1 root      20   0   44900   7360   2364 S  0.0  0.7   0:23.01 systemd                       
    2 root      20   0       0      0      0 S  0.0  0.0   0:00.00 kthreadd                      
    3 root      20   0       0      0      0 S  0.0  0.0   0:00.56 ksoftirqd/0                   
    5 root       0 -20       0      0      0 S  0.0  0.0   0:00.00 kworker/0:0H                  
    7 root      rt   0       0      0      0 S  0.0  0.0   0:00.00 migration/0                   
    8 root      20   0       0      0      0 S  0.0  0.0   0:00.00 rcu_bh                        
    9 root      20   0       0      0      0 S  0.0  0.0   0:00.00 rcuob/0                       
   10 root      20   0       0      0      0 S  0.0  0.0   0:03.61 rcu_sched                     
   11 root      20   0       0      0      0 R  0.0  0.0   0:06.97 rcuos/0                       
   12 root      rt   0       0      0      0 S  0.0  0.0   0:00.14 watchdog/0                    
   13 root       0 -20       0      0      0 S  0.0  0.0   0:00.00 khelper                       
   14 root      20   0       0      0      0 S  0.0  0.0   0:00.00 kdevtmpfs                     
   15 root       0 -20       0      0      0 S  0.0  0.0   0:00.00 netns                         
   16 root       0 -20       0      0      0 S  0.0  0.0   0:00.00 perf                          
   17 root       0 -20       0      0      0 S  0.0  0.0   0:00.00 writeback                     
   18 root       0 -20       0      0      0 S  0.0  0.0   0:00.00 kintegrityd                   
   19 root       0 -20       0      0      0 S  0.0  0.0   0:00.00 bioset                        
   20 root       0 -20       0      0      0 S  0.0  0.0   0:00.00 kblockd                       
   21 root       0 -20       0      0      0 S  0.0  0.0   0:00.00 md                            
   26 root      20   0       0      0      0 S  0.0  0.0   0:00.03 khungtaskd                    
   27 root      20   0       0      0      0 S  0.0  0.0   0:00.00 kswapd0                       
   28 root      25   5       0      0      0 S  0.0  0.0   0:00.00 ksmd                          
   29 root      39  19       0      0      0 S  0.0  0.0   0:00.56 khugepaged                    
   30 root      20   0       0      0      0 S  0.0  0.0   0:00.00 fsnotify_mark                 
   31 root       0 -20       0      0      0 S  0.0  0.0   0:00.00 crypto                        
   39 root       0 -20       0      0      0 S  0.0  0.0   0:00.00 kthrotld                      
   41 root       0 -20       0      0      0 S  0.0  0.0   0:00.00 kmpath_rdacd                  
   42 root       0 -20       0      0      0 S  0.0  0.0   0:00.00 kpsmoused                     
   43 root       0 -20       0      0      0 S  0.0  0.0   0:00.00 ipv6_addrconf                 
   63 root       0 -20       0      0      0 S  0.0  0.0   0:00.00 deferwq                       
  101 root      20   0       0      0      0 S  0.0  0.0   0:00.18 kauditd                       
  213 root       0 -20       0      0      0 S  0.0  0.0   0:00.00 ata_sff
```
 > 前五行是当前系统情况整体的统计信息区，下面我们看每一行信息的具体意义

第一行：任务队列信息

| 字段 | 说明 |
| :------------- | :------------- |
| 15:58:43  | Item Two       |
| up 2 days, 14:09 | 系统已经运行了2天14小时9分钟 |
| 1 users | 当前登录用户数 |
| load average: 0.00, 0.01, 0.05 | 系统负载，即任务队列的平均长度<br>三个数值分别为：1分钟、5分钟、15分钟前到现在的平均值 |
第二行：进程信息

| 字段 | 说明 |
| :------------- | :------------- |
| 77 total | 进程总数 |
| 3 running | 正在运行的进程数 |
| 74 sleeping | 睡眠的进程数 |
| 0 stopped | 停止的进程数 |
| 0 zombie | 僵尸进程数 |

第三行：CPU信息(如果有多个CPU的话信息会超过两行)

| 字段 | 说明 |
| :------------- | :------------- |
| 0.2 us | 系统用户进程使用CPU百分比 |
| 0.2 sy | 内核中的进程占用CPU百分比 |
| 0.0 ni	| 用户进程空间内改变过优先级的进程占用CPU百分比 |
| 99.5 id | 空闲CPU百分比 |
| 0.1 wa | IO等待占用CPU的百分比 |
| 0.0 hi | 硬中断（Hardware IRQ）占用CPU的百分比 |
| 0.0 si | 软中断（Software Interrupts）占用CPU的百分比 |
| 0.0 st | 虚拟机偷取时间 |

第四行：内存信息

| 字段 | 说明 |
| :------------- | :------------- |
| 1016916 total | 物理内存总量 |
| 533700 free | 空闲内存总量 |
| 90484 used | 使用的物理内存总量 |
| 392732 buff/cache | 用作内核缓存的内存量<br>和命令free –k一个意思 |

第五行：交换区信息

| 字段 | 说明 |
| :------------- | :------------- |
| 0 total | 交换区总量 |
| 0 free | 空闲交换区总量 |
| 0 used | 使用的交换区总量 |
| 768064 avail Mem | 总的可利用内存是多少 |

剩下的则是进程信息：

| 字段 | 说明 |
| :------------- | :------------- |
| PID | 进程ID |
| USER | 进程所有者 |
| PR | 进程优先级 |
| NI | nice值，负值表示高优先级，正值表示低优先级 |
| VIRT | 进程使用的虚拟内存总量，单位kb，VIRT=SWAP+RES |
| RES | 进程使用的、未被换出的物理内存大小，单位kb，RES=CODE+DATA |
| SHR | 共享内存大小，单位kb |
| S | 进程状态 |
| %CPU | 上次更新到现在的CPU时间占用百分比 |
| %MEM | 进程使用的物理内存百分比 |
| TIME+ | 进程使用的CPU时间总计，单位1/100秒 |
| COMMAND | 进程名称（命令名/命令行） |
### 查找某个服务的进程号
```
[root@VM_200_13_centos ~]# pgrep ssh
697
8435
```
### 以树状图显示进程间的关系
命令：pstree  
作用：以树状图显示进程，只显示进程的名字，且相同进程合并显示  
语法：pstree [选项]  

| 选项 | 说明 |
| :------------- | :------------- |
| -a | 显示命令行参数 |
| -A | 使用ASCII字符线 |
| -c | 不适用紧凑连接方式显示 |
| -h | 高亮所有的进程和其父进程的PID |
| -H | 高亮指定的进程和其父进程的PID |
| -g | 显示进程所属的用户组ID |
| -G | 使用VT100字符线 |
| -n | 根据PID排序 |
| -p | 显示PID |
| -s | 显示父进程 |
```
[root@VM_200_13_centos ~]# pstree -p
systemd(1)-+-acpid(474)
           |-agetty(1275)
           |-agetty(1276)
           |-atd(472)
           |-auditd(430)---{auditd}(448)
           |-barad_agent(1938)-+-barad_agent(1939)
           |                   `-barad_agent(1940)-+-{barad_agent}(1941)
           |                                       `-{barad_agent}(2009)
           |-chronyd(477)
           |-crond(473)
           |-dbus-daemon(454)
           |-lsmd(452)
           |-lvmetad(357)
           |-nginx(17698)---nginx(17699)
           |-rsyslogd(469)-+-{rsyslogd}(485)
           |               `-{rsyslogd}(486)
           |-sap1002(1853)
           |-sap1007(1861)
           |-secu-tcs-agent(1153)---sap1004(1859)
           |-sgagent(1922)---{sgagent}(1923)
           |-sshd(697)---sshd(8435)---bash(8437)---pstree(11096)
           |-systemd-journal(329)
           |-systemd-logind(458)
           |-systemd-udevd(359)
           `-tuned(695)-+-{tuned}(1329)
                        |-{tuned}(1330)
                        |-{tuned}(1332)
                        `-{tuned}(1335)
```
### 查询网络相关的进程
> 介绍netstat工具

命令：netstat  
作用：显示各种网络相关信息，如网络连接，路由表，接口状态 
语法：netstat [选项]

| 选项 | 说明 |
| :------------- | :------------- |
| -a或–all | 显示所有连接中的接口 |
| -c或–continuous | 持续列出网络状态 |
| -C或–cache | 显示路由器配置的快取信息 |
| -e或–extend | 显示网络其他相关信息 |
| -F或–fib | 显示FIB |
| -g或–groups | 显示多重广播功能群组组员名单 |
| -i或–interfaces | 显示网络界面信息表单 |
| -l或–listening | 显示监控中的服务器的接口 |
| -M或–masquerade | 显示伪装的网络连线 |
| -n或–numeric | 直接使用IP地址，而不通过域名服务器 |
| -N或–netlink或–symbolic | 显示网络硬件外围设备的符号连接名称 |
| -o或–timers | 显示计时器 |
| -p或–programs | 显示正在使用接口的程序识别码和程序名称 |
| -r或–route | 显示路由表 |
| -s或–statistice | 显示网络工作信息统计表 |
| -t或–tcp | 显示TCP传输协议的连接状况 |
| -u或–udp | 显示UDP传输协议的连接状况 |
| -v或–verbose | 显示指令执行过程 |
| -w或–raw | 显示RAW传输协议的连线状况 |
| -x或–unix | 此参数的效果和指定-A unix参数相同 |
| –ip或–inet | 此参数的效果和指定-A inet参数相同 |
**查看所有的TCP端口**
```
[root@VM_200_13_centos ~]# netstat -anlpt
Active Internet connections (servers and established)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name

tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN      17698/nginx: mast
er
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      697/sshd         

tcp        0      0 10.104.200.13:80        115.221.11.116:2137     ESTABLISHED 17699/nginx: work
er
tcp        0      0 10.104.200.13:80        115.221.14.249:4282     ESTABLISHED 17699/nginx: work
er
tcp        0      0 10.104.200.13:80        115.221.11.116:3583     ESTABLISHED 17699/nginx: work
er
tcp        0     52 10.104.200.13:22        182.150.21.5:6390       ESTABLISHED 8435/sshd: root@p
ts
tcp        0      0 10.104.200.13:80        115.221.14.139:4747     FIN_WAIT2   -                

tcp        0      0 10.104.200.13:80        115.221.11.116:3797     ESTABLISHED 17699/nginx: work
er
tcp        0      0 10.104.200.13:80        115.221.11.116:3276     FIN_WAIT2   -                

tcp        0      0 10.104.200.13:59586     10.249.80.72:9988       ESTABLISHED 1153/secu-tcs-age
nt
tcp        0      0 10.104.200.13:80        115.221.12.48:1670      FIN_WAIT2   -                

tcp6       0      0 :::22                   :::*                    LISTEN      697/sshd  
```
**查看某个服务的端口**
```
[root@VM_200_13_centos ~]# netstat -anlp | grep ssh
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      697/sshd            
tcp        0     52 10.104.200.13:22        182.150.21.5:6390       ESTABLISHED 8435/sshd: root@pts
tcp6       0      0 :::22                   :::*                    LISTEN      697/sshd            
unix  3      [ ]         STREAM     CONNECTED     12545    697/sshd             
unix  2      [ ]         DGRAM                    1401521  8435/sshd: root@pts
```
**查看所有的UDP端口**
```
[root@VM_200_13_centos ~]# netstat -anlpu          
Active Internet connections (servers and established)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    
udp        0      0 127.0.0.1:323           0.0.0.0:*                           477/chronyd         
udp6       0      0 ::1:323                 :::*                                477/chronyd
```
**查看nginx**
```
[root@VM_200_13_centos ~]# netstat -anlp | grep nginx
tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN      17698/nginx: master
tcp        0      0 10.104.200.13:80        115.221.11.116:4493     ESTABLISHED 17699/nginx: worker
tcp        0      0 10.104.200.13:80        115.221.12.48:2473      ESTABLISHED 17699/nginx: worker
unix  3      [ ]         STREAM     CONNECTED     488644   17698/nginx: master  
unix  3      [ ]         STREAM     CONNECTED     488643   17698/nginx: master
```
### 控制进程
命令：kill  
作用：通过信号的方式来控制进程  
语法：kill [选项] [进程号]

| 选项 | 说明 |
| :------------- | :------------- |
| -l | 若果不加指定的信号编号参数，则使用“-l”参数会列出全部的信号名称 |
| -a | 当处理当前进程时，不限制命令名和进程号的对应关系 |
| -p | 指定kill命令只打印相关进程的进程号，而不发送任何信号 |
| -u | 指定用户 |
| -s | 指定发送信号 |


+ kill命令可以带信号号码选项，也可以不带。如果没有信号号码，kill命令就会发出终止信号(15)，这个信号可以被进程捕获，使得进程在退出之前可以清理并释放资源  
也可以用kill向进程发送特定的信号，例如：kill -2 123  
它的效果等同于在前台运行PID为123的进程时按下Ctrl+C键。但是，普通用户只能使用不带signal参数的kill命令或最多使用-9信号  
+ kill可以带有进程ID号作为参数。当用kill向这些进程发送信号时，必须是这些进程的主人。如果试图撤销一个没有权限撤销的进程或撤销一个不存在的进程，就会得到一个错误信息
+ 可以向多个进程发信号或终止它们
+ 当kill成功地发送了信号后，shell会在屏幕上显示出进程的终止信息。有时这个信息不会马上显示，只有当按下Enter键使shell的命令提示符再次出现时，才会显示出来
+ 应注意，信号使进程强行终止，这常会带来一些副作用，如数据丢失或者终端无法恢复到正常状态  
发送信号时必须小心，只有在万不得已时，才用kill信号(9)，因为进程不能首先捕获它  
要撤销所有的后台作业，可以输入kill 0，因为有些在后台运行的命令会启动多个进程，跟踪并找到所有要杀掉的进程的PID是件很麻烦的事  
这时，使用kill 0来终止所有由当前shell启动的进程，是个有效的方法

**查看所有的进程信号**
```
[root@VM_200_13_centos ~]# kill -l
 1) SIGHUP       2) SIGINT       3) SIGQUIT      4) SIGILL       5) SIGTRAP
 6) SIGABRT      7) SIGBUS       8) SIGFPE       9) SIGKILL     10) SIGUSR1
11) SIGSEGV     12) SIGUSR2     13) SIGPIPE     14) SIGALRM     15) SIGTERM
16) SIGSTKFLT   17) SIGCHLD     18) SIGCONT     19) SIGSTOP     20) SIGTSTP
21) SIGTTIN     22) SIGTTOU     23) SIGURG      24) SIGXCPU     25) SIGXFSZ
26) SIGVTALRM   27) SIGPROF     28) SIGWINCH    29) SIGIO       30) SIGPWR
31) SIGSYS      34) SIGRTMIN    35) SIGRTMIN+1  36) SIGRTMIN+2  37) SIGRTMIN+3
38) SIGRTMIN+4  39) SIGRTMIN+5  40) SIGRTMIN+6  41) SIGRTMIN+7  42) SIGRTMIN+8
43) SIGRTMIN+9  44) SIGRTMIN+10 45) SIGRTMIN+11 46) SIGRTMIN+12 47) SIGRTMIN+13
48) SIGRTMIN+14 49) SIGRTMIN+15 50) SIGRTMAX-14 51) SIGRTMAX-13 52) SIGRTMAX-12
53) SIGRTMAX-11 54) SIGRTMAX-10 55) SIGRTMAX-9  56) SIGRTMAX-8  57) SIGRTMAX-7
58) SIGRTMAX-6  59) SIGRTMAX-5  60) SIGRTMAX-4  61) SIGRTMAX-3  62) SIGRTMAX-2
```
**关闭进程**
```
[root@VM_200_13_centos ~]# vim test.txt

[1]+  Stopped                 vim test.txt
[root@VM_200_13_centos ~]# ps -aux | grep vim
root     14797  0.0  0.4  49768  4848 pts/0    T    17:38   0:00 vim test.txt
root     14859  0.0  0.0   9036   668 pts/0    R+   17:39   0:00 grep --color=auto vim
[root@VM_200_13_centos ~]# kill -9 14797
[root@VM_200_13_centos ~]# ps -aux | grep vim
root     14870  0.0  0.0   9036   672 pts/0    R+   17:39   0:00 grep --color=auto vim
[1]+  Killed                  vim test.txt
```
**重启进程**
```
[root@VM_200_13_centos ~]# ps -aux | grep nginx
root      8582  0.0  0.0   9036   672 pts/0    R+   00:40   0:00 grep --color=auto nginx
root     17698  0.0  0.0  20468   604 ?        Ss   Aug15   0:00 nginx: master process nginx
nginx    17699  0.0  0.1  22980  1612 ?        S    Aug15   0:01 nginx: worker process
[root@VM_200_13_centos ~]# kill -1 17698
```
### 终止所有进程
> linux系统中的killall命令用于杀死指定名字的进程（kill processes by name）  
我们可以使用kill命令杀死指定进程PID的进程，如果要找到我们需要杀死的进程，我们还需要在之前使用ps等命令再配合grep来查找进程  
而killall把这两个过程合二为一，是一个很好用的命令

命令：killall  
作用：用来结束同名的的所有进程  
语法：killall [选项] 进程名称  

| 选项 | 说明 |
| :------------- | :------------- |
| -Z | 只杀死拥有context的进程 |
| -e | 要求匹配进程名称 |
| -I | 不区分大小写匹配进程名称 |
| -g | 杀死进程组而不是进程 |
| -i | 交互模式，杀死进程前先询问用户 |
| -l | 列出所有的已知信号名称 |
| -q | 不输出警告信息 |
| -s | 发送指定的信号 |
| -w | 等待进程死亡 |
| -v | 报告信号是否成功发送 |
| -r | 使用正则表达式匹配进程名 |
| -u | 只杀死用户运行的进程 |

**关闭所有httpd进程**
 ```
[root@VM_200_13_centos ~]# yum -y install httpd
[root@VM_200_13_centos ~]# systemctl start httpd
[root@VM_200_13_centos3 ~]# ps -aux | grep httpd
root      39962  0.0  0.1 226128  5052 ?        Ss   08:38   0:00 /usr/sbin/httpd -DFOREGROUND
apache    39974  0.0  0.0 228212  3128 ?        S    08:39   0:00 /usr/sbin/httpd -DFOREGROUND
apache    39975  0.0  0.0 228212  3128 ?        S    08:39   0:00 /usr/sbin/httpd -DFOREGROUND
apache    39976  0.0  0.0 228212  3128 ?        S    08:39   0:00 /usr/sbin/httpd -DFOREGROUND
apache    39977  0.0  0.0 228212  3128 ?        S    08:39   0:00 /usr/sbin/httpd -DFOREGROUND
apache    39978  0.0  0.0 228212  3128 ?        S    08:39   0:00 /usr/sbin/httpd -DFOREGROUND
root      39985  0.0  0.0 112644   956 pts/1    S+   08:39   0:00 grep --color=auto httpd
[root@VM_200_13_centos3 ~]# killall httpd
 ```
## 虚拟文件系统
> Linux一切皆文件，设备（文件）可以通过读写来操作

/proc是内存中有关 **系统进程** 的信息  
/sys是有关 **系统内核以及驱动** 的信息

查看当前CPU信息
```
[root@VM_200_13_centos ~]# cat /proc/cpuinfo
processor       : 0
vendor_id       : GenuineIntel
cpu family      : 6
model           : 63
model name      : Intel(R) Xeon(R) CPU E5-26xx v3
stepping        : 2
microcode       : 0x1
cpu MHz         : 2294.686
cache size      : 4096 KB
physical id     : 0
siblings        : 1
core id         : 0
cpu cores       : 1
apicid          : 0
initial apicid  : 0
fpu             : yes
fpu_exception   : yes
cpuid level     : 13
wp              : yes
flags           : fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush mmx fxsr sse sse2 ss ht syscall
 nx lm constant_tsc rep_good nopl eagerfpu pni pclmulqdq ssse3 fma cx16 pcid sse4_1 sse4_2 x2apic movbe popcnt tsc_deadline_timer
 aes xsave avx f16c rdrand hypervisor lahf_lm abm xsaveopt
bogomips        : 4589.37
clflush size    : 64
cache_alignment : 64
address sizes   : 40 bits physical, 48 bits virtual
power management:
```
开启内核转发功能
```
[root@VM_200_13_centos ~]# echo "1" > /proc/sys/net/ipv4/ip_forward
[root@VM_200_13_centos ~]# cat /proc/sys/net/ipv4/ip_forward
1
```
## 后台进程与前台进程
**后台进程:**
也叫守护进程（Daemon），是运行在后台的一种特殊进程,守护的意思就是不受终端控制。Linux的大多数服务器就是用守护进程实现的比如，Web服务器httpd等
**前台进程：**
用户使用的有控制终端的进程
**跟系统任务相关的几个命令：fg、bg、jobs、&、ctrl+z**
| Header One     | Header Two     |
| :------------- | :------------- |
| & | 用在一个命令的最后，可以把这个命令放到后台执行 |
| ctrl+z | 将一个正在前台执行的命令放到后台，并且暂停 |
| jobs | 查看当前有多少在后台运行的进程 |
| fg | 将后台中的命令调至前台继续运行<br>如果后台中有多个命令，可以用 fg %jobnumber将选中的命令调出<br>%jobnumber是通过jobs命令查到的后台正在执行的命令的序号(不是pid) |
| bg | 将一个在后台暂停的命令，变成继续执行<br>如果后台中有多个命令，可以用bg %jobnumber将选中的命令调出<br>%jobnumber是通过jobs命令查到的后台正在执行的命令的序号(不是pid) |
**恢复被挂起的进程**
```
[root@VM_200_13_centos ~]# vim test.txt

[1]+  Stopped                 vim test.txt
[root@VM_200_13_centos ~]# ps -aux |grep vim
root     13516  0.1  0.4  49756  4804 pts/0    T    17:18   0:00 vim test.txt
root     13518  0.0  0.0   9036   672 pts/0    R+   17:18   0:00 grep --color=auto vim
[root@VM_200_13_centos ~]# jobs
[1]+  Stopped                 vim test.txt
[root@VM_200_13_centos ~]# fg %1
vim test.txt
```
## 进程的优先级管理
**优先级取值范围为[-20,19]，越小优先级越高, 默认优先级是0**  
### 设置进程的优先级
命令：nice  
作用：设置进程的优先级  
语法：nice [选项] 命令  
说明：nice的选项只有一个-n，参数后面跟上[-20,19]范围内的值，不使用该参数则以10为值设置nice值
```
[root@VM_200_13_centos ~]# nice vim v.txt

[1]+  Stopped                 nice vim v.txt
[root@VM_200_13_centos ~]# ps -aux | grep vim
root     10029  0.0  0.4  49748  4800 pts/0    TN   01:01   0:00 vim v.txt
root     10038  0.0  0.0   9036   672 pts/0    R+   01:01   0:00 grep --color=auto vim
```
**使用top命令查看上面操作的nice值**
```
[root@VM_200_13_centos ~]# top -n 1 -p 10029
top - 01:03:25 up 2 days, 23:13,  1 user,  load average: 0.00, 0.01, 0.05
Tasks:   1 total,   0 running,   0 sleeping,   1 stopped,   0 zombie
%Cpu(s):  0.2 us,  0.2 sy,  0.0 ni, 99.5 id,  0.1 wa,  0.0 hi,  0.0 si,  0.0 st
KiB Mem :  1016916 total,   511816 free,    93860 used,   411240 buff/cache
KiB Swap:        0 total,        0 free,        0 used.   763804 avail Mem

  PID USER      PR  NI    VIRT    RES    SHR S %CPU %MEM     TIME+ COMMAND                       
10029 root      30  10   49748   4800   2484 T  0.0  0.5   0:00.02 vim                             
```
### 调整进程优先级
命令：renice  
作用：改变一个正在运行的进程的优先级  
语法：renice [-n] 优先级值 [-gpu] identifier..

| 选项 | 说明 |
| :------------- | :------------- |
| -n | 修改运行中进程的优先级，后面跟上[-20,19]范围内的值 |
| -g | 强制将后续的参数解释为进程所属用户的属组GID |
| -p | 强制将后续的参数解释为进程PID，这个参数是默认使用的 |
| -u | 强制将后续的参数解释为进程所属用户的UID |
```
[root@VM_200_13_centos ~]# ps -aux | grep vim
root     10029  0.0  0.4  49748  4800 pts/0    TN   01:01   0:00 vim v.txt
root     10909  0.0  0.0   9036   668 pts/0    R+   01:15   0:00 grep --color=auto vim
[root@VM_200_13_centos ~]# renice -n -20 10029
10029 (process ID) old priority 10, new priority -20
```
## 远程会话管理工具
>Screen是Linux下的一款远程会话管理工具，可以在多个进程之间多路复用一个物理终端的全屏窗口管理器  
它可以创建多个会话（Session），每个会话中可以创建多个窗口（Window）  
每个窗口中可以运行单独的任务，并且互相之间不受影响，还可以方便快速的在不同的窗口和会话之间切换  
Screen可以看作是窗口管理器的命令行界面版本。它提供了统一的管理多个会话的界面和相应的功能

+ 会话恢复  
只要Screen本身没有终止，在其内部运行的会话都可以恢复  
这一点对于远程登录的用户特别有用——即使网络连接中断，用户也不会失去对已经打开的命令行会话的控制  
只要再次登录到主机上执行screen -r就可以恢复会话的运行  
同样在暂时离开的时候，也可以执行分离命令detach，在保证里面的程序正常运行的情况下让Screen挂起（切换到后台）
+ 多窗口
在Screen环境下，所有的会话都独立的运行，并拥有各自的编号、输入、输出和窗口缓存  
用户可以通过快捷键在不同的窗口下切换，并可以自由的重定向各个窗口的输入和输出  
Screen实现了基本的文本操作，如复制粘贴等；还提供了类似滚动条的功能，可以查看窗口状况的历史记录  
窗口还可以被分区和命名，还可以监视后台窗口的活动
+ 会话共享  
Screen可以让一个或多个用户从不同终端多次登录一个会话，并共享会话的所有特性（比如可以看到完全相同的输出）  
它同时提供了窗口访问权限的机制，可以对窗口进行密码保护
### 使用场景
在VPS中执行一些非常耗时的任务时（如下载，压缩，解压缩，编译，安装等）  
我们通常是单独开一个远程终端窗口来执行这个任务，且在任务执行过程中不能关闭这个窗口或者中断连接，否则正在执行的任务会被终止掉  
而有了screen，我们可以在一个窗口中安装程序，然后在另一个窗口中下载文件，再在第三个窗口中编译程序  
只需要一个SSH连接就可以同时执行这三个任务，还可以方便的在不同会话或窗口中切换  
即使因为意外导致窗口关闭或者连接中断，也不会影响这三个任务的执行
### 使用说明
命令：screen  
作用：可以在多个进程之间多路复用一个物理终端的全屏窗口管理器  
语法：screen [选项] [命令]  

| 选项 | 说明 |
| :------------- | :------------- |
| -ls | 查看所有screen会话 |
| -r | 重新连接指定id的会话（使用-ls可以查看到会话id）|
| -L | 打开输出日志 |
| -s | 指定新建的screen会话执行的shell |
| -S | 新建一个会话，后面跟上会话名称 |
| -d | 远程断开一个screen会话 |

**实际使用**
使用Xshell连接服务器，进入screen
```
[root@VM_200_13_centos ~]# screen
```
在screen窗口执行updatedb模拟执行一个耗时很长的操作
在screen窗口键入Ctrl+a+d模拟中断,Screen会给出detached提示
```
[detached from 13137.pts-0.VM_200_13_centos]
```
退出Xshell或重连，再查看所有的screen会话
```
[root@VM_200_13_centos ~]# screen -ls
There is a screen on:
	13137.pts-0.VM_200_13_centos	(Detached)
1 Socket in /var/run/screen/S-root.
```
重连之前的screen
```
[root@VM_200_13_centos ~]# screen -r 1
```


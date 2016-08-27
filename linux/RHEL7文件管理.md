## Linux系统目录结构
### 主要目录说明

  | 目录 | 说明     |
  | :------------- | :------------- |
  |    /    | 通常称为根分区<br>所有的文件和目录的起始点<br>只有root用户对此目录拥有写权限  |
  |    /home    |    普通用户的宿主目录    |
  |    /root    |    超级用户的宿主目录    |
  |    /dev    |    存放设备相关文件<br>在Linux中一切皆为文件<br>终端设备/磁盘/光驱都被看作是文件<br>例如:/dev/sda    |
  |    /usr    |    存放应用程序<br>usr表示的是unix software source<br>/usr/src 源代码存放目录    |
  |    /etc    |    存放配置文件<br>包含所有应用程序的配置文件<br>也包含启动/关闭某个特定程序的脚本<br>例如:<br>/etc/passwd<br>/etc/init.d/network  |
  |    /boot    |    存放Linux系统启动时需要加载的文件<br>Kerner/grub等文件都存放在此    |
  |    /lib    |    存放系统的库文件<br>以.a为后缀的是静态库文件<br>以.so为后缀的是动态库文件<br>静态库在编译时加载到二进制文件中<br>动态库在运行时加载到进程的内存空间中<br>简而言之 库文件是为了让程序能够正常编译运行<br>其作用类似windows中的.dll文件<br>几乎所有的应用程序都需要用到这些共享库文件    |
  |    /var    |    是一个可增长的目录<br>包含经常变动的文件<br>例如:<br>/var/log 系统日志文件<br>/var/lib 包文件    |
  |    /tmp    |    存放临时文件<br>不能将重要数据存放在此处<br>因为有系统级别的计划任务定期清理该目录下的文件    |
  |    /proc    |    该目录是一个虚拟的目录<br>它是系统内存的映射<br>可以通过直接访问这个目录来获取系统信息<br>查看内存信息命令: cat /proc/meminfo<br>查看CPU信息命令: cat /proc/cpuinfo   |
  |    /bin    |    命令<br>此目录包含二进制可执行文件<br>存放普通用户可执行的程序    |
  |    /sbin    |   系统命令<br>此目录中的命令主要供系统管理员使用<br>以便进行系统维护<br>存放超级用户可执行的程序     |
**小结：**  

- 大多数情况下，本地管理员将额外的软件安装在/usr/local目录下，并符号链接在/usr/local/bin下的主执行程序
- 系统的所有设置在/etc目录下
- 不要修改根目录/或/usr目录下的任何内容 目录最好和Linux发布时保持一致
- 大多数工具和应用程序安装在目录/bin /usr/bin /sbin
- 所有的文件在单一的目录树下 没有windows下所谓的驱动符:C盘 D盘 ...



## 路径
在平时使用计算机时要找到需要的文件就必须知道文件的位置，而表示文件的位置的方式就是路径
### 绝对路径
在Linux中 绝对路径是从/开始的，比如/usr，/etc/passwd  
如果一个路径是从/开始的就一定是绝对路径  
```
[root@administrator ~]# pwd
/root
[root@administrator ~]# cd /etc/sysconfig/network-scripts/
[root@administrator network-scripts]# pwd
/etc/sysconfig/network-scripts
```
### 相对路径
相对路径是以.或者..开始的  
.表示用户当前操作所处的位置 即当前目录  
..表示上级目录
```
[root@administrator ~]# cd /etc/sysconfig/network-scripts/
[root@administrator network-scripts]# pwd
/etc/sysconfig/network-scripts
[root@administrator network-scripts]# cd .
[root@administrator network-scripts]# pwd
/etc/sysconfig/network-scripts
[root@administrator network-scripts]# cd ..
[root@administrator sysconfig]# pwd
/etc/sysconfig
[root@administrator sysconfig]# cd ./
[root@administrator sysconfig]# pwd
/etc/sysconfig
[root@administrator sysconfig]# cd ./..
[root@administrator etc]# pwd
/etc
[root@administrator etc]# cd ..
[root@administrator /]# pwd
/
```
## 文件操作
>介绍文件和文件夹的相关操作

### 创建文件
命令：touch  
作用：创建空文件  
语法：touch 文件名  
```
[root@administrator ~]# touch example.txt
[root@administrator ~]# ls
anaconda-ks.cfg  Documents  example.txt           Music     Public     Videos
Desktop          Downloads  initial-setup-ks.cfg  Pictures  Templates
```
### 创建目录
命令：mkdir
作用：创建目录
语法：mkdir 目录名
```
[root@administrator ~]# mkdir example
[root@administrator ~]# ls
anaconda-ks.cfg  Documents  example               Music     Public     Videos
Desktop          Downloads  initial-setup-ks.cfg  Pictures  Templates
```
**-p 创建多级目录**
```
[root@administrator ~]# mkdir -p example/test
[root@administrator ~]# ls
anaconda-ks.cfg  Documents  example               Music     Public     Videos
Desktop          Downloads  initial-setup-ks.cfg  Pictures  Templates
[root@administrator ~]# ll example/
total 0
drwxr-xr-x 2 root root 6 Aug  8 11:09 test
[root@administrator ~]# cd example/
[root@administrator example]# ls
test
```
### 查看文件
#### 非分页查看  
命令：cat  
作用：查看文件内容  
语法：cat 文件名
#### 分页查看  
  - more  
    命令：more  
    作用：分页查看文件内容  
    语法：more 文件名  
    操作：按下回车刷新一行,按下空格刷新一屏,按q退出
  - less  
    命令：less  
    作用：分页查看文件内容  
    语法：less 文件名  
    操作：按q退出 使用光标键可以向上翻页
  - head  
    命令：head  
    作用：从第一行开始查看文件 默认显示前10条  
    语法：head [参数] 文件名  
    参数：
    -n 显示n行  
  - tail  
    命令：tail  
    作用：从最后一行开始查看文件内容 默认显示10条  
    语法：tail [参数] 文件名  
    参数：  
    -n 显示n行  
    -f 动态显示数据（常用来查看日志 比如查看tomcat日志）
  - 组合使用  
    场景：只查看文件的第2行内容
    先查看文件initial-setup-ks.cfg前两行内容

    ```
    [root@administrator ~]# head -2 initial-setup-ks.cfg
    #version=DEVEL
    # X Window System configuration information
    ```
    使用管道将上述操作的结果作为tail命令的输入
    ```
    [root@administrator ~]# head -2 initial-setup-ks.cfg | tail -1
    # X Window System configuration information
    ```

### 文件复制
命令：cp  
作用：复制文件  
语法：cp [参数] 源文件 目标文件  
参数：-r 连同子目录和文件一起复制
```
[root@administrator ~]# cp initial-setup-ks.cfg  test.cfg
[root@administrator ~]# ls
anaconda-ks.cfg  Documents  initial-setup-ks.cfg  Pictures  Templates  Videos
Desktop          Downloads  Music                 Public    test.cfg
[root@administrator ~]# mkdir -p example/test/
[root@administrator ~]# touch example/test/test.txt
[root@administrator ~]# cp -r example/ cptest
[root@administrator ~]# ls
anaconda-ks.cfg  Documents  initial-setup-ks.cfg  Public     Videos
cptest           Downloads  Music                 Templates
Desktop          example    Pictures              test.cfg
[root@administrator ~]# ls cptest/
test
```
### 重命名
命令：mv  
作用：重命名目标文件或文件夹  
语法：mv 源文件或文件夹 目标文件或文件夹
```
[root@administrator ~]# ls
anaconda-ks.cfg  Documents  initial-setup-ks.cfg  Public     Videos
cptest           Downloads  Music                 Templates
Desktop          example    Pictures              test.cfg
[root@administrator ~]# mv test.cfg  example.cfg
[root@administrator ~]# ls
anaconda-ks.cfg  Documents  example.cfg           Pictures   Videos
cptest           Downloads  initial-setup-ks.cfg  Public
Desktop          example    Music                 Templates
```
### 删除文件或目录
命令：rm  
作用：删除文件或目录  
语法：rm [参数]  文件或目录  
参数：-r 递归删除(可以删除目录和目录里面的东西) -f 强行删除
```
[root@administrator ~]# ls
anaconda-ks.cfg  Documents  example.cfg           Pictures   Videos
cptest           Downloads  initial-setup-ks.cfg  Public
Desktop          example    Music                 Templates
[root@administrator ~]# rm example.cfg
rm: remove regular file ‘example.cfg’? y
[root@administrator ~]# ls
anaconda-ks.cfg  Desktop    Downloads  initial-setup-ks.cfg  Pictures  Templates
cptest           Documents  example    Music  
[root@administrator ~]# rm -f example.cfg
[root@administrator ~]# ls
anaconda-ks.cfg  Desktop    Downloads  initial-setup-ks.cfg  Pictures  Templates
cptest           Documents  example    Music                 Public    Videos
[root@administrator ~]# rm -rf example/
[root@administrator ~]# ls
anaconda-ks.cfg  Desktop    Downloads             Music     Public     Videos
cptest           Documents  initial-setup-ks.cfg  Pictures  Templates
```
## xfs文件系统的备份和恢复
REDHAT7使用XFS文件系统，XFS提供了xfsdump和xfsrestore工具协助备份XFS文件系统中的数据  。
xfsdump按inode顺序备份一个XFS文件系统 ，与传统的UNIX文件系统不同，XFS不需要在dump前被卸载对使用中的XFS文件系统做dump就可以保证镜像的一致性。  
这与XFS对快照的实现不同，XFS的dump和restore的过程是可以被中断然后继续的，无须冻结文件系统。  
xfsdump甚至提供了高性能的多线程备份操作——它把一次dump拆分成多个数据流，每个数据流可以被发往不同的目的地。  
xfsdump的备份级别有以下两种，默认为即完全备份：  
0       完全备份  
1       增量备份  
### 使用虚拟机创建实验环境
>为了确保系统不被误操作导致无法正常使用创建一个安全的实验环境

#### 为虚拟机添加一块虚拟硬盘  

**添加一块硬盘/dev/sdb**
```
[root@administrator ~]# ls /dev/sd*
/dev/sda  /dev/sda1  /dev/sda2  /dev/sdb
```

**使用添加的硬盘创建一个1G的新分区**  
**输入命令fdisk /dev/sdb 设置大小时输入+1G 根据提示最后输入w即可 默认值可以不用输入**  
由于输出的内容过长fdisk /dev/sdb命令的输出就不贴上来了  
创建完成后查看
```
[root@administrator ~]# ls /dev/sdb*
/dev/sdb  /dev/sdb1
```

**上面的操作将添加的硬盘分成了两部分，一部分为刚才创建的1G新分区一部分为剩下的19G**
#### 格式化分区并进行挂载  
**格式化分区为xfs分区**
```
[root@administrator ~]# mkfs.xfs /dev/sdb1
meta-data=/dev/sdb1              isize=256    agcount=4, agsize=65536 blks
         =                       sectsz=512   attr=2, projid32bit=1
         =                       crc=0        finobt=0
data     =                       bsize=4096   blocks=262144, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0 ftype=0
log      =internal log           bsize=4096   blocks=2560, version=2
         =                       sectsz=512   sunit=0 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0
```
**创建挂载点目录sdb1**
```
[root@administrator ~]# mkdir /sdb1
[root@administrator ~]# ls /
bin   dev  home  lib64  mnt  proc  run   sdb1  sys  usr
boot  etc  lib   media  opt  root  sbin  srv   tmp  var
```
**挂载分区到上面创建的挂载点目录sdb1**
```
[root@administrator ~]# mount /dev/sdb1 /sdb1/
```
**查看刚才挂载的分区**
```
[root@administrator ~]# mount | tail -1
/dev/sdb1 on /sdb1 type xfs (rw,relatime,attr2,inode64,noquota)
```

### 备份
**先准备测试文件**
```
[root@administrator ~]# cp /etc/passwd /sdb1/
[root@administrator ~]# mkdir /sdb1/dumptest
[root@administrator ~]# ls /sdb1
dumptest  passwd
```
命令：xfsdump  
作用：对分区或者文件进行备份  
语法：xfsdump -f 备份文件路径 备份源
```
[root@administrator ~]# xfsdump -f ~/sdb1.dump /sdb1
xfsdump: using file dump (drive_simple) strategy
xfsdump: version 3.1.4 (dump format 3.0) - type ^C for status and control

 ============================= dump label dialog ==============================

please enter label for this dump session (timeout in 300 sec)
 -> sdb1
session label entered: "sdb1"

 --------------------------------- end dialog ---------------------------------

xfsdump: level 0 dump of administrator:/sdb1
xfsdump: dump date: Mon Aug  8 13:08:52 2016
xfsdump: session id: 97f9f6d0-3816-4791-9568-facc0cdf49d1
xfsdump: session label: "sdb1"
xfsdump: ino map phase 1: constructing initial dump list
xfsdump: ino map phase 2: skipping (no pruning necessary)
xfsdump: ino map phase 3: skipping (only one dump stream)
xfsdump: ino map construction complete
xfsdump: estimated dump size: 25536 bytes
xfsdump: /var/lib/xfsdump/inventory created

 ============================= media label dialog =============================

please enter label for media in drive 0 (timeout in 300 sec)
 -> sdb1
media label entered: "sdb1"

 --------------------------------- end dialog ---------------------------------

xfsdump: creating dump session media file 0 (media 0, file 0)
xfsdump: dumping ino map
xfsdump: dumping directories
xfsdump: dumping non-directory files
xfsdump: ending media file
xfsdump: media file size 24240 bytes
xfsdump: dump size (non-dir files) : 2592 bytes
xfsdump: dump complete: 9 seconds elapsed
xfsdump: Dump Summary:
xfsdump:   stream 0 /root/sdb1.dump OK (success)
xfsdump: Dump Status: SUCCESS
```
**查看备份文件**
```
[root@administrator ~]# cat sdb1.dump
xFSdump0�XW�������8G��h��
                           �I�administratorsdb1Rpsd��.�B٨
��v�}1/sdb1/dev/sdb1xfsBkm��J�����{�?�������d����A�"W��N)b��W��@5W�#W��@5W�#�:�� passwd�%' dumptest����X��\�A�W��@5W�#W��@5W�#W��@5W�#����휧?����W��ͅW��ͅW��ͅ
����root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
adm:x:3:4:adm:/var/adm:/sbin/nologin
lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
sync:x:5:0:sync:/sbin:/bin/sync
shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
halt:x:7:0:halt:/sbin:/sbin/halt
mail:x:8:12:mail:/var/spool/mail:/sbin/nologin
operator:x:11:0:operator:/root:/sbin/nologin
games:x:12:100:games:/usr/games:/sbin/nologin
ftp:x:14:50:FTP User:/var/ftp:/sbin/nologin
nobody:x:99:99:Nobody:/:/sbin/nologin
avahi-autoipd:x:170:170:Avahi IPv4LL Stack:/var/lib/avahi-autoipd:/sbin/nologin
systemd-bus-proxy:x:999:997:systemd Bus Proxy:/:/sbin/nologin
systemd-network:x:998:996:systemd Network Management:/:/sbin/nologin
dbus:x:81:81:System message bus:/:/sbin/nologin
polkitd:x:997:995:User for polkitd:/:/sbin/nologin
unbound:x:996:993:Unbound DNS resolver:/etc/unbound:/sbin/nologin
tss:x:59:59:Account used by the trousers package to sandbox the tcsd daemon:/dev/null:/sbin/nologin
colord:x:995:992:User for colord:/var/lib/colord:/sbin/nologin
usbmuxd:x:113:113:usbmuxd user:/:/sbin/nologin
geoclue:x:994:991:User for geoclue:/var/lib/geoclue:/sbin/nologin
saslauth:x:993:76:Saslauthd user:/run/saslauthd:/sbin/nologin
libstoragemgmt:x:992:990:daemon account for libstoragemgmt:/var/run/lsm:/sbin/nologin
abrt:x:173:173::/etc/abrt:/sbin/nologin
setroubleshoot:x:991:988::/var/lib/setroubleshoot:/sbin/nologin
rpc:x:32:32:Rpcbind Daemon:/var/lib/rpcbind:/sbin/nologin
rtkit:x:172:172:RealtimeKit:/proc:/sbin/nologin
chrony:x:990:987::/var/lib/chrony:/sbin/nologin
radvd:x:75:75:radvd user:/:/sbin/nologin
qemu:x:107:107:qemu user:/:/sbin/nologin
rpcuser:x:29:29:RPC Service User:/var/lib/nfs:/sbin/nologin
nfsnobody:x:65534:65534:Anonymous NFS User:/var/lib/nfs:/sbin/nologin
pulse:x:171:171:PulseAudio System Daemon:/var/run/pulse:/sbin/nologin
gdm:x:42:42::/var/lib/gdm:/sbin/nologin
gnome-initial-setup:x:989:984::/run/gnome-initial-setup/:/sbin/nologin
avahi:x:70:70:Avahi mDNS/DNS-SD Stack:/var/run/avahi-daemon:/sbin/nologin
postfix:x:89:89::/var/spool/postfix:/sbin/nologin
sshd:x:74:74:Privilege-separated SSH:/var/empty/sshd:/sbin/nologin
ntp:x:38:38::/etc/ntp:/sbin/nologin
tcpdump:x:72:72::/:/sbin/nologin
courier:x:1000:1000:courier:/home/courier:/bin/bash
��������
```
>可以看出备份文件进行了加密导致乱码

**使用参数-L和-M进行无交互式的备份**
```
[root@administrator ~]# xfsdump -f ~/sdb1.dump0 /sdb1 -L sdb1 -M sdb1
xfsdump: using file dump (drive_simple) strategy
xfsdump: version 3.1.4 (dump format 3.0) - type ^C for status and control
xfsdump: WARNING: most recent level 0 dump was interrupted, but not resuming that dump since resume (-R) option not specified
xfsdump: level 0 dump of administrator:/sdb1
xfsdump: dump date: Mon Aug  8 13:14:23 2016
xfsdump: session id: 2ff3a409-406f-4d93-a054-8af7f70ea278
xfsdump: session label: "sdb1"
xfsdump: ino map phase 1: constructing initial dump list
xfsdump: ino map phase 2: skipping (no pruning necessary)
xfsdump: ino map phase 3: skipping (only one dump stream)
xfsdump: ino map construction complete
xfsdump: estimated dump size: 25536 bytes
xfsdump: creating dump session media file 0 (media 0, file 0)
xfsdump: dumping ino map
xfsdump: dumping directories
xfsdump: dumping non-directory files
xfsdump: ending media file
xfsdump: media file size 24240 bytes
xfsdump: dump size (non-dir files) : 2592 bytes
xfsdump: dump complete: 0 seconds elapsed
xfsdump: Dump Summary:
xfsdump:   stream 0 /root/sdb1.dump0 OK (success)
xfsdump: Dump Status: SUCCESS
[root@administrator ~]# ls
anaconda-ks.cfg  Documents             Music     sdb1.dump   Videos
cptest           Downloads             Pictures  sdb1.dump0
Desktop          initial-setup-ks.cfg  Public    Templates
```
**备份目标分区中的单个文件或者目录**
```
[root@administrator ~]# xfsdump -f  ~/pwd.dump -s passwd /sdb1 -L pwd -M pwd
xfsdump: using file dump (drive_simple) strategy
xfsdump: version 3.1.4 (dump format 3.0) - type ^C for status and control
xfsdump: level 0 dump of administrator:/sdb1
xfsdump: dump date: Mon Aug  8 13:19:03 2016
xfsdump: session id: 0b6f8c2b-d9df-4713-a0b7-d991d6277f61
xfsdump: session label: "pwd"
xfsdump: ino map phase 1: constructing initial dump list
xfsdump: ino map phase 2: skipping (no pruning necessary)
xfsdump: ino map phase 3: skipping (only one dump stream)
xfsdump: ino map construction complete
xfsdump: estimated dump size: 25216 bytes
xfsdump: creating dump session media file 0 (media 0, file 0)
xfsdump: dumping ino map
xfsdump: dumping directories
xfsdump: dumping non-directory files
xfsdump: ending media file
xfsdump: media file size 23960 bytes
xfsdump: dump size (non-dir files) : 2592 bytes
xfsdump: dump complete: 5 seconds elapsed
xfsdump: Dump Summary:
xfsdump:   stream 0 /root/pwd.dump OK (success)
xfsdump: Dump Status: SUCCESS
[root@administrator ~]# ls
anaconda-ks.cfg  Desktop    Downloads             Music     Public    Templates
cptest           Documents  initial-setup-ks.cfg  Pictures  pwd.dump  Videos
```
### 恢复
命令：xfsrestore  
作用：对分区或者文件进行恢复  
命令：xfsrestore -f 备份源 需要恢复的目标分区或文件路径  
**删除/sdb1分区下的所有文件**
```
[root@administrator ~]# rm -rf /sdb1/*
[root@administrator ~]# ls /sdb1/
```
**使用之前的备份进行恢复**
```
[root@administrator ~]# xfsrestore -f sdb1.dump /sdb1/
xfsrestore: using file dump (drive_simple) strategy
xfsrestore: version 3.1.4 (dump format 3.0) - type ^C for status and control
xfsrestore: searching media for dump
xfsrestore: examining media file 0
xfsrestore: dump description:
xfsrestore: hostname: administrator
xfsrestore: mount point: /sdb1
xfsrestore: volume: /dev/sdb1
xfsrestore: session time: Mon Aug  8 13:25:18 2016
xfsrestore: level: 0
xfsrestore: session label: "sdb1"
xfsrestore: media label: "sdb1"
xfsrestore: file system id: 426b6d86-0782-4a1e-92e2-c3f310ba7b87
xfsrestore: session id: 228b15a9-7c1f-46ed-9fae-c64fc8e465a5
xfsrestore: media id: d670fd47-fbcb-4845-8d7e-7993cf2103ae
xfsrestore: using online session inventory
xfsrestore: searching media for directory dump
xfsrestore: reading directories
xfsrestore: 2 directories and 2 entries processed
xfsrestore: directory post-processing
xfsrestore: restoring non-directory files
xfsrestore: restore complete: 0 seconds elapsed
xfsrestore: Restore Summary:
xfsrestore:   stream 0 /root/sdb1.dump OK (success)
xfsrestore: Restore Status: SUCCESS
[root@administrator ~]# ls /sdb1/
dumptest  passwd
[root@administrator ~]# cat /sdb1/passwd
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
adm:x:3:4:adm:/var/adm:/sbin/nologin
lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
sync:x:5:0:sync:/sbin:/bin/sync
shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
halt:x:7:0:halt:/sbin:/sbin/halt
mail:x:8:12:mail:/var/spool/mail:/sbin/nologin
operator:x:11:0:operator:/root:/sbin/nologin
games:x:12:100:games:/usr/games:/sbin/nologin
ftp:x:14:50:FTP User:/var/ftp:/sbin/nologin
nobody:x:99:99:Nobody:/:/sbin/nologin
avahi-autoipd:x:170:170:Avahi IPv4LL Stack:/var/lib/avahi-autoipd:/sbin/nologin
systemd-bus-proxy:x:999:997:systemd Bus Proxy:/:/sbin/nologin
systemd-network:x:998:996:systemd Network Management:/:/sbin/nologin
dbus:x:81:81:System message bus:/:/sbin/nologin
polkitd:x:997:995:User for polkitd:/:/sbin/nologin
unbound:x:996:993:Unbound DNS resolver:/etc/unbound:/sbin/nologin
tss:x:59:59:Account used by the trousers package to sandbox the tcsd daemon:/dev/null:/sbin/nologin
colord:x:995:992:User for colord:/var/lib/colord:/sbin/nologin
usbmuxd:x:113:113:usbmuxd user:/:/sbin/nologin
geoclue:x:994:991:User for geoclue:/var/lib/geoclue:/sbin/nologin
saslauth:x:993:76:Saslauthd user:/run/saslauthd:/sbin/nologin
libstoragemgmt:x:992:990:daemon account for libstoragemgmt:/var/run/lsm:/sbin/nologin
abrt:x:173:173::/etc/abrt:/sbin/nologin
setroubleshoot:x:991:988::/var/lib/setroubleshoot:/sbin/nologin
rpc:x:32:32:Rpcbind Daemon:/var/lib/rpcbind:/sbin/nologin
rtkit:x:172:172:RealtimeKit:/proc:/sbin/nologin
chrony:x:990:987::/var/lib/chrony:/sbin/nologin
radvd:x:75:75:radvd user:/:/sbin/nologin
qemu:x:107:107:qemu user:/:/sbin/nologin
rpcuser:x:29:29:RPC Service User:/var/lib/nfs:/sbin/nologin
nfsnobody:x:65534:65534:Anonymous NFS User:/var/lib/nfs:/sbin/nologin
pulse:x:171:171:PulseAudio System Daemon:/var/run/pulse:/sbin/nologin
gdm:x:42:42::/var/lib/gdm:/sbin/nologin
gnome-initial-setup:x:989:984::/run/gnome-initial-setup/:/sbin/nologin
avahi:x:70:70:Avahi mDNS/DNS-SD Stack:/var/run/avahi-daemon:/sbin/nologin
postfix:x:89:89::/var/spool/postfix:/sbin/nologin
sshd:x:74:74:Privilege-separated SSH:/var/empty/sshd:/sbin/nologin
ntp:x:38:38::/etc/ntp:/sbin/nologin
tcpdump:x:72:72::/:/sbin/nologin
courier:x:1000:1000:courier:/home/courier:/bin/bash
```
>可以看出文件已经恢，查看文件显示正常

**恢复单个文件：先删除/sdb1分区中的passwd文件，再使用备份进行恢复**
```
[root@administrator ~]# ls /sdb1/
dumptest  passwd
[root@administrator ~]# rm -rf /sdb1/passwd
[root@administrator ~]# ls /sdb1/
dumptest
[root@administrator ~]# xfsrestore -f sdb1.dump  -s passwd /sdb1/
xfsrestore: using file dump (drive_simple) strategy
xfsrestore: version 3.1.4 (dump format 3.0) - type ^C for status and control
xfsrestore: searching media for dump
xfsrestore: examining media file 0
xfsrestore: dump description:
xfsrestore: hostname: administrator
xfsrestore: mount point: /sdb1
xfsrestore: volume: /dev/sdb1
xfsrestore: session time: Mon Aug  8 13:25:18 2016
xfsrestore: level: 0
xfsrestore: session label: "sdb1"
xfsrestore: media label: "sdb1"
xfsrestore: file system id: 426b6d86-0782-4a1e-92e2-c3f310ba7b87
xfsrestore: session id: 228b15a9-7c1f-46ed-9fae-c64fc8e465a5
xfsrestore: media id: d670fd47-fbcb-4845-8d7e-7993cf2103ae
xfsrestore: using online session inventory
xfsrestore: searching media for directory dump
xfsrestore: reading directories
xfsrestore: 2 directories and 2 entries processed
xfsrestore: directory post-processing
xfsrestore: restoring non-directory files
xfsrestore: restore complete: 0 seconds elapsed
xfsrestore: Restore Summary:
xfsrestore:   stream 0 /root/sdb1.dump OK (success)
xfsrestore: Restore Status: SUCCESS
[root@administrator ~]# ls /sdb1/
dumptest  passwd
```
## 附录
### less和more的区别
more：不支持后退，但几乎不需要加参数，空格键是向下翻页，Enter键是向下翻一行，在不需要后退的情况下比较方便  
less：支持前后翻滚，既可以向上翻页（pageup按键），也可以向下翻页（pagedown按键） ，空格键是向下翻页，Enter键是向下翻一行


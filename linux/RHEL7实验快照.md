## 配置虚拟机连接网络
首先确保NetworkManager服务正常运行
```
[root@administrator ~]# systemctl status  NetworkManager
● NetworkManager.service - Network Manager
   Loaded: loaded (/usr/lib/systemd/system/NetworkManager.service; enabled; vendor preset: enabled)
   Active: active (running) since Sun 2016-08-07 00:21:59 EDT; 2min 58s ago
 Main PID: 1192 (NetworkManager)
   CGroup: /system.slice/NetworkManager.service
           └─1192 /usr/sbin/NetworkManager --no-daemon

Aug 07 00:22:09 administrator NetworkManager[1192]: <info>  (virbr0-nic): dev...
Aug 07 00:22:09 administrator NetworkManager[1192]: <info>  (virbr0): device ...
Aug 07 00:22:09 administrator NetworkManager[1192]: <info>  (virbr0-nic): dev...
Aug 07 00:22:09 administrator NetworkManager[1192]: <info>  (virbr0): device ...
Aug 07 00:22:09 administrator NetworkManager[1192]: <info>  NetworkManager st...
Aug 07 00:22:09 administrator NetworkManager[1192]: <info>  (virbr0): Activat...
Aug 07 00:22:09 administrator NetworkManager[1192]: <info>  (virbr0-nic): dev...
Aug 07 00:22:09 administrator NetworkManager[1192]: <info>  (virbr0-nic): Act...
Aug 07 00:22:09 administrator NetworkManager[1192]: <info>  (virbr0-nic): lin...
Aug 07 00:22:14 administrator NetworkManager[1192]: <info>  (virbr0-nic): lin...
Hint: Some lines were ellipsized, use -l to show in full.

```
PING本机
```
[root@administrator ~]# ping 127.0.0.1
PING 127.0.0.1 (127.0.0.1) 56(84) bytes of data.
64 bytes from 127.0.0.1: icmp_seq=1 ttl=64 time=0.092 ms
64 bytes from 127.0.0.1: icmp_seq=2 ttl=64 time=0.091 ms
64 bytes from 127.0.0.1: icmp_seq=3 ttl=64 time=0.094 ms
64 bytes from 127.0.0.1: icmp_seq=4 ttl=64 time=0.097 ms
64 bytes from 127.0.0.1: icmp_seq=5 ttl=64 time=0.094 ms
64 bytes from 127.0.0.1: icmp_seq=6 ttl=64 time=0.092 ms
64 bytes from 127.0.0.1: icmp_seq=7 ttl=64 time=0.097 ms
64 bytes from 127.0.0.1: icmp_seq=8 ttl=64 time=0.100 ms
64 bytes from 127.0.0.1: icmp_seq=9 ttl=64 time=0.100 ms
64 bytes from 127.0.0.1: icmp_seq=10 ttl=64 time=0.058 ms
^C
--- 127.0.0.1 ping statistics ---
10 packets transmitted, 10 received, 0% packet loss, time 9002ms
rtt min/avg/max/mdev = 0.058/0.091/0.100/0.015 ms
```
PING百度
```
[root@administrator ~]# ping baidu.com
ping: unknown host baidu.com
```

PING百度无法PING通，下面开始配置
两种配置方式：修改配置文件/使用图形界面工具 

1. 修改网卡配置文件

  打开配置文件
  ```
  [root@administrator ~]# vim /etc/sysconfig/network-scripts/ifcfg-eno16777736
  ```
  修改前的原配置文件
  ```
  TYPE=Ethernet
  BOOTPROTO=dhcp
  DEFROUTE=yes
  PEERDNS=yes
  PEERROUTES=yes
  IPV4_FAILURE_FATAL=no
  IPV6INIT=yes
  IPV6_AUTOCONF=yes
  IPV6_DEFROUTE=yes
  IPV6_PEERDNS=yes
  IPV6_PEERROUTES=yes
  IPV6_FAILURE_FATAL=no
  NAME=eno16777736
  UUID=ea5c55cd-2fcf-4ed8-a31d-90e86fe56925
  DEVICE=eno16777736
  ONBOOT=no
  ```
  修改后的配置文件
  ```
  TYPE=Ethernet
  #static/none/dhcp 桥接模式设置为dhcp即可
  BOOTPROTO=dhcp
  DEFROUTE=yes
  PEERDNS=yes
  PEERROUTES=yes
  IPV4_FAILURE_FATAL=no
  IPV6INIT=yes
  IPV6_AUTOCONF=yes
  IPV6_DEFROUTE=yesgi
  IPV6_PEERDNS=yes
  IPV6_PEERROUTES=yes
  IPV6_FAILURE_FATAL=no
  NAME=eno16777736
  UUID=ea5c55cd-2fcf-4ed8-a31d-90e86fe56925
  DEVICE=eno16777736
  #启用
  ONBOOT=yes
  ```
  重启网络服务
  ```
  [root@administrator ~]# systemctl restart network
  ```
  再次PING百度，虚拟机已经能正常上网
  ```
  [root@administrator ~]# ping baiu.com
  PING baiu.com (222.76.218.234) 56(84) bytes of data.
  64 bytes from baiu.com (222.76.218.234): icmp_seq=1 ttl=119 time=52.1 ms
  64 bytes from baiu.com (222.76.218.234): icmp_seq=2 ttl=119 time=133 ms
  64 bytes from baiu.com (222.76.218.234): icmp_seq=3 ttl=119 time=52.1 ms
  64 bytes from baiu.com (222.76.218.234): icmp_seq=4 ttl=119 time=50.7 ms
  64 bytes from baiu.com (222.76.218.234): icmp_seq=5 ttl=119 time=54.0 ms
  64 bytes from baiu.com (222.76.218.234): icmp_seq=6 ttl=119 time=53.3 ms
  ^C
  --- baiu.com ping statistics ---
  6 packets transmitted, 6 received, 0% packet loss, time 5009ms
  rtt min/avg/max/mdev = 50.727/65.890/133.046/30.051 ms
  ```
  设置为固定IP上网
  ```
  C:\Users\admin>ipconfig

  Windows IP Configuration

  Wireless LAN adapter Local Area Connection* 2:

     Media State . . . . . . . . . . . : Media disconnected
     Connection-specific DNS Suffix  . :

  Ethernet adapter VMware Network Adapter VMnet1:

     Connection-specific DNS Suffix  . :
     Link-local IPv6 Address . . . . . : fe80::55e1:aa5d:1373:732d%19
     IPv4 Address. . . . . . . . . . . : 192.168.19.1
     Subnet Mask . . . . . . . . . . . : 255.255.255.0
     Default Gateway . . . . . . . . . :

  Ethernet adapter VMware Network Adapter VMnet8:

     Connection-specific DNS Suffix  . :
     Link-local IPv6 Address . . . . . : fe80::89ce:2f34:2be5:2f22%20
     IPv4 Address. . . . . . . . . . . : 192.168.72.1
     Subnet Mask . . . . . . . . . . . : 255.255.255.0
     Default Gateway . . . . . . . . . :

  Wireless LAN adapter Wi-Fi:

     Connection-specific DNS Suffix  . : DHCP HOST
     Link-local IPv6 Address . . . . . : fe80::549b:37ee:8982:a1a0%6
     IPv4 Address. . . . . . . . . . . : 192.168.0.104
     Subnet Mask . . . . . . . . . . . : 255.255.255.0
     Default Gateway . . . . . . . . . : 192.168.0.1

  Tunnel adapter isatap.DHCP HOST:

     Media State . . . . . . . . . . . : Media disconnected
     Connection-specific DNS Suffix  . : DHCP HOST

  Tunnel adapter Teredo Tunneling Pseudo-Interface:

     Connection-specific DNS Suffix  . :
     IPv6 Address. . . . . . . . . . . : 2001:0:9d38:6abd:2cd8:158e:5422:ffa6
     Link-local IPv6 Address . . . . . : fe80::2cd8:158e:5422:ffa6%4
     Default Gateway . . . . . . . . . : ::

  Tunnel adapter isatap.{D7907800-61F5-4BB5-8D20-E1F1ECB51622}:

     Media State . . . . . . . . . . . : Media disconnected
     Connection-specific DNS Suffix  . :

  Tunnel adapter isatap.{077C386B-1B8A-408E-9D76-EF1AAA015440}:

     Media State . . . . . . . . . . . : Media disconnected
     Connection-specific DNS Suffix  . :

  C:\Users\admin>
  ```
  从上面看出宿主机的网段在0网段:192.168.0.1（默认网关）
  设置在这个网段内即可（确保你设置的ip没有在局域网内已经被使用）
  再次修改配置文件为如下配置：
  ```
  TYPE=Ethernet
  BOOTPROTO=none
  IPADDR=192.168.0.254
  PREFIX=32
  GATEWAY=192.168.0.1
  DNS1=114.114.114.114
  DEFROUTE=yes
  IPV4_FAILURE_FATAL=no
  IPV6INIT=yes
  IPV6_AUTOCONF=yes
  IPV6_DEFROUTE=yes
  IPV6_PEERDNS=yes
  IPV6_PEERROUTES=yes
  IPV6_FAILURE_FATAL=no
  NAME=eno16777736
  UUID=ea5c55cd-2fcf-4ed8-a31d-90e86fe56925  
  DEVICE=eno16777736
  ONBOOT=yes
  ```
  配置说明
  ```
  BOOTPROTO：IP地址类型 值为none/static/dhcp，配置为静态ip设置为none或者static
  IPADDR：IP地址
  ONBOOT：是否启动
  PREFIX：掩码长度
  DNS1：DNS SERVER
  GATEWAY：默认网关
  ```
  修改完毕后重启网络服务并PING百度已能正常上网
  ```
  [root@administrator ~]# systemctl restart network
  [root@administrator ~]# ping baidu.com
  PING baidu.com (180.149.132.47) 56(84) bytes of data.
  64 bytes from 180.149.132.47: icmp_seq=1 ttl=54 time=66.2 ms
  64 bytes from 180.149.132.47: icmp_seq=2 ttl=54 time=65.3 ms
  64 bytes from 180.149.132.47: icmp_seq=3 ttl=54 time=60.6 ms
  ^C
  --- baidu.com ping statistics ---
  3 packets transmitted, 3 received, 0% packet loss, time 2004ms
  rtt min/avg/max/mdev = 60.692/64.111/66.296/2.466 ms
  ```

2. 使用图形界面工具NetworkManager TUI

  ```
  [root@administrator ~]# nmtui
  ```
  然后根据图形界面傻瓜式操作即可

## 关闭防火墙并设置开机自动关闭
查看防火墙状态
```
[root@administrator ~]# systemctl status firewalld.service
● firewalld.service - firewalld - dynamic firewall daemon
   Loaded: loaded (/usr/lib/systemd/system/firewalld.service; enabled; vendor preset: enabled)
   Active: active (running) since Sun 2016-08-07 02:44:34 EDT; 6min ago
 Main PID: 1025 (firewalld)
   CGroup: /system.slice/firewalld.service
           └─1025 /usr/bin/python -Es /usr/sbin/firewalld --nofork --nopid

Aug 07 02:44:28 administrator systemd[1]: Starting firewalld - dynamic firew....
Aug 07 02:44:34 administrator systemd[1]: Started firewalld - dynamic firewa....
Hint: Some lines were ellipsized, use -l to show in full.
```
关闭防火墙
```
[root@administrator ~]# systemctl stop firewalld
```
设置开机自动关闭
```
[root@administrator ~]# systemctl disable firewall
```
再次查看防火墙状态发现已经成功关闭
```
[root@administrator ~]# systemctl status firewalld.service
● firewalld.service - firewalld - dynamic firewall daemon
   Loaded: loaded (/usr/lib/systemd/system/firewalld.service; enabled; vendor preset: enabled)
   Active: inactive (dead) since Sun 2016-08-07 02:53:09 EDT; 1min 31s ago
 Main PID: 1025 (code=exited, status=0/SUCCESS)

Aug 07 02:44:28 administrator systemd[1]: Starting firewalld - dynamic firew....
Aug 07 02:44:34 administrator systemd[1]: Started firewalld - dynamic firewa....
Aug 07 02:53:08 administrator systemd[1]: Stopping firewalld - dynamic firew....
Aug 07 02:53:09 administrator systemd[1]: Stopped firewalld - dynamic firewa....
Hint: Some lines were ellipsized, use -l to show in full.
```
## 关闭selinux并设置开机自动关闭
临时关闭：本次操作后关闭，下次重启依然会开启
```
[root@administrator ~]# setenforce 0
```
永久关闭
```
[root@administrator ~]# vim /etc/sysconfig/selinux
```
原配置文件
```
# This file controls the state of SELinux on the system.
# SELINUX= can take one of these three values:
#     enforcing - SELinux security policy is enforced.
#     permissive - SELinux prints warnings instead of enforcing.
#     disabled - No SELinux policy is loaded.
SELINUX=enforcing
# SELINUXTYPE= can take one of three two values:
#     targeted - Targeted processes are protected,
#     minimum - Modification of targeted policy. Only selected processes are protected.
#     mls - Multi Level Security protection.
SELINUXTYPE=targeted
```
修改为
```
# This file controls the state of SELinux on the system.
# SELINUX= can take one of these three values:
#     enforcing - SELinux security policy is enforced.
#     permissive - SELinux prints warnings instead of enforcing.
#     disabled - No SELinux policy is loaded.
SELINUX=disabled
# SELINUXTYPE= can take one of three two values:
#     targeted - Targeted processes are protected,
#     minimum - Modification of targeted policy. Only selected processes are protected.
#     mls - Multi Level Security protection.
SELINUXTYPE=targeted
```
## 设置光盘开机自动挂载
```
[root@administrator ~]# vim /etc/fstab
```
原配置文件
```
#
# /etc/fstab
# Created by anaconda on Sun Aug  7 08:42:09 2016
#
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
/dev/mapper/rhel-root   /                       xfs     defaults        0 0
UUID=b2cf3412-1b3b-48b7-a16a-d971b50b1660 /boot                   xfs     defaults        0 0
/dev/mapper/rhel-swap   swap                    swap    defaults        0 0
```
在最后添加下面的配置保存退出
```
/dev/cdrom              /mnt                    iso9660 defaults        0 0
```
验证并查看是否挂载成功
```
[root@administrator ~]# mount -a
mount: /dev/sr0 is write-protected, mounting read-only
[root@administrator ~]# df
Filesystem            1K-blocks    Used Available Use% Mounted on
/dev/mapper/rhel-root  49746196 3244372  46501824   7% /
devtmpfs                1001236       0   1001236   0% /dev
tmpfs                   1016776     144   1016632   1% /dev/shm
tmpfs                   1016776    9144   1007632   1% /run
tmpfs                   1016776       0   1016776   0% /sys/fs/cgroup
/dev/sda1                508588  143104    365484  29% /boot
tmpfs                    203356       4    203352   1% /run/user/42
tmpfs                    203356      16    203340   1% /run/user/0
/dev/sr0                3947824 3947824         0 100% /mnt
```
## 配置本地yum源
配置本地yum源之前先挂载系统盘  
切换到/etc/yum.repos.d/目录下，删除该目录下的所有文件(请勿在真机操作：先备份)
```
[root@administrator yum.repos.d]# cd /etc/yum.repos.d/
[root@administrator yum.repos.d]# rm -rf *
[root@administrator yum.repos.d]# ll
total 0
```
创建yum源配置文件
```
[root@administrator yum.repos.d]# vim redhat.repo
```
添加以下内容 保存退出
```
[local-repository]
name=local-repository
baseurl=file:///mnt
enabled=1
gpgcheck=0
```
查看以下刚才编辑的内容
```
administrator yum.repos.d]# cat local.repo
[local-repository]
name=local-repository
baseurl=file:///mnt
enabled=1
gpgcheck=0
```
清空yum缓存
```
[root@administrator yum.repos.d]# yum clean all
Loaded plugins: langpacks, product-id, search-disabled-repos, subscription-
              : manager
This system is not registered to Red Hat Subscription Management. You can use subscription-manager to register.
Cleaning repos: local-repository
Cleaning up everything
```
生成缓存列表（软件包的数量上千个，只截取了前面几个）
```
[root@administrator yum.repos.d]# yum list
Loaded plugins: langpacks, product-id, search-disabled-repos, subscription-
              : manager
This system is not registered to Red Hat Subscription Management. You can use subscription-manager to register.
local-repository                                         | 4.1 kB     00:00     
(1/2): local-repository/group_gz                           | 136 kB   00:00     
(2/2): local-repository/primary_db                         | 3.6 MB   00:00     
Installed Packages
GConf2.x86_64                          3.2.6-8.el7              @anaconda/7.2   
ModemManager.x86_64                    1.1.0-8.git20130913.el7  @anaconda/7.2   
ModemManager-glib.x86_64               1.1.0-8.git20130913.el7  @anaconda/7.2   
NetworkManager.x86_64                  1:1.0.6-27.el7           @anaconda/7.2   
NetworkManager-adsl.x86_64             1:1.0.6-27.el7           @anaconda/7.2   
```
统计上面操作的结果行数：大约有4000个以上的软件包
```
[root@administrator yum.repos.d]# yum list | wc -l
4734
```
## 创建快照
上述步骤全部操作完毕后，使用VMWare Workstation创建一个快照供以后使用




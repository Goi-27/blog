>本文介绍RHEL7的软件包管理 

RHEL7下主要有RPM和YUM这两种包管理：

+ YUM使用简单但需要联网，YUM会去网上的YUM包源去获取所需要的软件包并获取该包依赖的其他包
+ RPM的需要的操作精度比较细，需要我们做的事情比较多，需要自己安装依赖

## RPM
![安装流程](http://img.blog.csdn.net/20160815020608569)
> 下面会以虚拟机进行RMP包管理

命令：rpm
作用：安装、卸载、升级和管理软件
语法：rpm [选项]
**准备实验环境：挂载光驱到/mnt目录**
```
[root@administrator ~]# mount /dev/cdrom /mnt
mount: /dev/sr0 is write-protected, mounting read-only
[root@administrator ~]# df
Filesystem            1K-blocks    Used Available Use% Mounted on
/dev/mapper/rhel-root  49746196 3297648  46448548   7% /
devtmpfs                1001236       0   1001236   0% /dev
tmpfs                   1016776     156   1016620   1% /dev/shm
tmpfs                   1016776    9156   1007620   1% /run
tmpfs                   1016776       0   1016776   0% /sys/fs/cgroup
/dev/sda1                508588  143104    365484  29% /boot
tmpfs                    203356       4    203352   1% /run/user/42
tmpfs                    203356       8    203348   1% /run/user/1000
/dev/sr0                3947824 3947824         0 100% /mnt
```
### 安装软件
> 安装zsh-5.0.2-14.el7.x86_64.rpm

![文件名称说明](http://img.blog.csdn.net/20160815020639523)
| 选项 | 说明 |
| :------------- | :------------- |
| -i | 安装软件包 |
| --nodeps | 不验证软件包依赖 |
| -v | 输出详细信息 |
| -h | 软件包安装的时候列出哈希标记 |

```
[root@administrator ~]# rpm -ivh /mnt/Packages/zsh-5.0.2-14.el7.x86_64.rpm
warning: /mnt/Packages/zsh-5.0.2-14.el7.x86_64.rpm: Header V3 RSA/SHA256 Signature, key ID fd431d51: NOKEY
Preparing...                          ################################# [100%]
Updating / installing...
   1:zsh-5.0.2-14.el7                 ################################# [100%]
```
查看安装好的zsh（shell）：
```
[root@administrator ~]# chsh -l
/bin/sh
/bin/bash
/sbin/nologin
/usr/bin/sh
/usr/bin/bash
/usr/sbin/nologin
/bin/tcsh
/bin/csh
/bin/zsh
```
**rpm支持网络安装：**
```
[root@administrator ~]# rpm -ivh http://mirrors.kernel.org/centos/7/cloud/x86_64/openstack-kilo/centos-release-openstack-kilo-1-2.el7.noarch.rpm
```
### 查询软件
> 使用rpm查询软件时下列选项要和-q选项组合使用

通过rpm命令查询一个rpm包是否安装了，也是要通过rpm 数据库来完成的  
所以我们要经常用下面的两个命令来初始化rpm 数据库
```
[root@administrator ~]# rpm --initdb 
[root@administrator ~]# rpm --rebuilddb 
```
**注：这两个参数是极为有用，有时rpm 系统出了问题，不能安装和查询，大多是这里出了问题**

| 选项 | 说明 |
| :------------- | :------------- |
| -a | 查询所有已安装的软件包 |
| -p | 查询软件包（通常用来看下还未安装的软件包）|
| -l | 显示软件包中的文件列表 |
| -f | 查询文件所属软件包 |
| -d | 查询软件包的文档所在位置 |
| -c | 显示被标注为配置文件的文件列表 |

**通常可以配合管道 | more 来使用，使得结果更易读**
```
[root@administrator ~]# rpm -qa zsh
zsh-5.0.2-14.el7.x86_64
[root@administrator ~]# rpm -ivh /mnt/Packages/zsh-5.0.2-14.el7.x86_64.rpm
warning: /mnt/Packages/zsh-5.0.2-14.el7.x86_64.rpm: Header V3 RSA/SHA256 Signature, key ID fd431d51: NOKEY
Preparing...                          ################################# [100%]
	package zsh-5.0.2-14.el7.x86_64 is already installed
[root@administrator ~]# which zsh
/usr/bin/zsh
[root@administrator ~]# rpm -qf /usr/bin/zsh 
zsh-5.0.2-14.el7.x86_64
[root@administrator ~]# rpm -qf `which zsh`
zsh-5.0.2-14.el7.x86_64
```
**如果使用rpm安装因为依赖没有安装而出错，可以使用下面的命令查询依赖，然后安装需要的依赖再执行该包的安装**
```
[root@administrator ~]# rpm -qpR /mnt/Packages/zsh-5.0.2-14.el7.x86_64.rpm 
```
### 升级软件
| 选项 | 说明 |
| :------------- | :------------- |
| -U | 升级软件<br>加上--oldpackage则是降级软件 |

```
[root@administrator ~]# rpm -Uvh /mnt/Packages/lrzsz-0.12.20-36.el7.x86_64.rpm 
warning: /mnt/Packages/lrzsz-0.12.20-36.el7.x86_64.rpm: Header V3 RSA/SHA256 Signature, key ID fd431d51: NOKEY
Preparing...                          ################################# [100%]
	package lrzsz-0.12.20-36.el7.x86_64 is already installed
```
### 卸载软件
| 选项 | 说明 |
| :------------- | :------------- |
| -e | 卸载软件 |
```
[root@administrator ~]# rpm -evh zsh
Preparing...                          ################################# [100%]
Cleaning up / removing...
   1:zsh-5.0.2-14.el7                 ################################# [100%]
```
如果有依赖关系，您也可以用--nodeps 忽略依赖的检查来删除  
但尽可能不要这么做，最好用软件包管理器systerm-config-packages来删除或者添加软件  
**使用chsh -l查看发现zsh已经被卸载掉**
```
[root@administrator ~]# chsh -l
/bin/sh
/bin/bash
/sbin/nologin
/usr/bin/sh
/usr/bin/bash
/usr/sbin/nologin
/bin/tcsh
/bin/csh
```
### 从软件包抽取文件
>当在linux中，修复系统时明确知道是丢了哪个文件，但是在此时强制安装丢失文件相应的rpm包时又会出现很多依赖关系提示。  
我们只需要这个丢掉的文件而已，可以找到丢失文件的相关rpm包，然后用cpio及rpm2cpio组合，把文件提取出来，然后再复制到丢失文件的相应位置

命令：rpm2cpio  
作用：将rpm包中的文件抽取成cpio文件  
语法：rpm2cpio rpm包文件名

命令：cpio  
作用：cpio命令是通过 **重定向** 的方式将文件进行打包备份，还原恢复的工具。它可以解压以“.cpio”或者“.tar”结尾的文件  
语法：cpio [选项]  文件名或者设备名 路径  

| 选项| 说明 |
| :------------- | :------------- |
| -o | 将文件拷贝打包成文件或者将文件输出到设备上 |
| -i | 将打包文件解压或者将设备上的备份还原到系统 |
| -t | 查看cpio打包的文件内容或者输出到设备上的文件内容 |
| -v | 显示打包过程中的文件名称 |
| -d | 在cpio还原文件的过程中，自动的建立相应的目录 | 
| -c | 一种较新的存储方式 |
| -B | 让默认的Blocks可以增大到5120 bytes，默认Blocks为512 bytes<br>这样做的好处是可以加快存取速度 |

**将/etc下的所有普通文件都备份到宿主目录下的etc.cpio文件中**
```
[root@administrator ~]# find /etc/ -type f | cpio -ocvB > etc.cpio 
```
### 源代码安装
.src.rpm结尾的文件，这些文件是由软件的源代码包装而成的  
**用户要安装这类RPM软件包，必须使用命令： ** 
rpm　--recompile　vim-4.6-4.src.rpm   ＃这个命令会把源代码解包并编译、安装它，如果用户使用命令  
rpm　--rebuild　vim-4.6-4.src.rpm　　＃在安装完成后，还会把编译生成的可执行文件重新包装成i386.rpm 的RPM软件包
## YUM
>基于RPM包管理，能够从指定的服务器自动下载RPM包并且安装，可以自动处理依赖性关系  
并且一次安装所有依赖的软件包，无须繁琐地一次次下载、安装  
基于C/S架构

### 配置文件
**添加本地yum源**
```
[root@administrator ~]# ls /etc/yum.repos.d/
local.repo
[root@administrator ~]# rm -rf /etc/yum.repos.d/*
[root@administrator ~]# vim /etc/yum.repos.d/rhel7.repo
[root@administrator ~]# cat /etc/yum.repos.d/rhel7.repo
[rhel7-yum] 
name=local
baseurl=file:///mnt
enabled=1
gpgcheck=0
```
### 清空缓存和生成列表
**清空缓存**
```
[root@administrator ~]# yum clean all
Loaded plugins: langpacks, product-id, search-disabled-repos, subscription-manager
This system is not registered to Red Hat Subscription Management. You can use subscription-manager to register.
Cleaning repos: rhel7-yum
Cleaning up everything
```
**生成列表**
```
[root@administrator ~]# yum list
```
### 主要操作
>以下操作在centos云主机上完成

+ 安装
```
[root@VM_200_13_centos ~]# yum install -y httpd
Loaded plugins: langpacks, product-id, search-disabled-repos, subscription-manager
This system is not registered to Red Hat Subscription Management. You can use subscription-manager to register.
Resolving Dependencies
--> Running transaction check
---> Package httpd.x86_64 0:2.4.6-40.el7 will be installed
--> Processing Dependency: /etc/mime.types for package: httpd-2.4.6-40.el7.x86_64
--> Running transaction check
---> Package mailcap.noarch 0:2.1.41-2.el7 will be installed
--> Finished Dependency Resolution

Dependencies Resolved

================================================================================================================================
 Package                     Arch                       Version                             Repository                     Size
================================================================================================================================
Installing:
 httpd                       x86_64                     2.4.6-40.el7                        rhel7-yum                     1.2 M
Installing for dependencies:
 mailcap                     noarch                     2.1.41-2.el7                        rhel7-yum                      31 k

Transaction Summary
================================================================================================================================
Install  1 Package (+1 Dependent package)

Total download size: 1.2 M
Installed size: 3.8 M
Downloading packages:
--------------------------------------------------------------------------------------------------------------------------------
Total                                                                                            40 MB/s | 1.2 MB  00:00:00     
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
Warning: RPMDB altered outside of yum.
  Installing : mailcap-2.1.41-2.el7.noarch                                                                                  1/2 
  Installing : httpd-2.4.6-40.el7.x86_64                                                                                    2/2 
rhel7-yum/productid                                                                                      | 1.6 kB  00:00:00     
  Verifying  : mailcap-2.1.41-2.el7.noarch                                                                                  1/2 
  Verifying  : httpd-2.4.6-40.el7.x86_64                                                                                    2/2 

Installed:
  httpd.x86_64 0:2.4.6-40.el7                                                                                                   

Dependency Installed:
  mailcap.noarch 0:2.1.41-2.el7                                                                                                 

Complete!
```
+ 检测升级 
```
[root@VM_200_13_centos ~]# yum check-update
```
+ 升级
```
[root@VM_200_13_centos ~]# yum update gcc
Loaded plugins: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
No packages marked for update
```
+ 软件包查询
```
[root@VM_200_13_centos ~]# yum list gcc   
Loaded plugins: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
Installed Packages
gcc.x86_64   
```
+ 软件包信息
```
[root@VM_200_13_centos ~]# yum info gcc
Loaded plugins: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
Installed Packages
Name        : gcc
Arch        : x86_64
Version     : 4.8.5
Release     : 4.el7
Size        : 37 M
Repo        : installed
From repo   : os
Summary     : Various compilers (C, C++, Objective-C, Java, ...)
URL         : http://gcc.gnu.org
License     : GPLv3+ and GPLv3+ with exceptions and GPLv2+ with exceptions and LGPLv2+ and BSD
Description : The gcc package contains the GNU Compiler Collection version 4.8.
            : You'll need this package in order to compile C code.

```
+ 卸载
```
[root@VM_200_13_centos ~]# yum -y remove httpd
Loaded plugins: fastestmirror, langpacks
Resolving Dependencies
--> Running transaction check
---> Package httpd.x86_64 0:2.4.6-40.el7.centos.4 will be erased
--> Finished Dependency Resolution

Dependencies Resolved

================================================================================================================================
 Package                  Arch                      Version                                   Repository                   Size
================================================================================================================================
Removing:
 httpd                    x86_64                    2.4.6-40.el7.centos.4                     @updates                    9.4 M

Transaction Summary
================================================================================================================================
Remove  1 Package

Installed size: 9.4 M
Downloading packages:
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  Erasing    : httpd-2.4.6-40.el7.centos.4.x86_64                                                                           1/1 
  Verifying  : httpd-2.4.6-40.el7.centos.4.x86_64                                                                           1/1 

Removed:
  httpd.x86_64 0:2.4.6-40.el7.centos.4                                                                                          

Complete!
```
+ 组安装
```
[root@VM_200_13_centos ~]# yum groupinstall -y "Security Tools"
Loaded plugins: fastestmirror, langpacks
There is no installed groups file.
Maybe run: yum groups mark convert (see man yum)
Loading mirror speeds from cached hostfile
Resolving Dependencies
--> Running transaction check
---> Package scap-security-guide.noarch 0:0.1.25-3.el7.centos.0.1 will be installed
--> Processing Dependency: openscap-scanner >= 1.2.5 for package: scap-security-guide-0.1.25-3.el7.centos.0.1.noarch
--> Processing Dependency: xml-common for package: scap-security-guide-0.1.25-3.el7.centos.0.1.noarch
--> Running transaction check
---> Package openscap-scanner.x86_64 0:1.2.9-5.el7_2 will be installed
--> Processing Dependency: openscap(x86-64) = 1.2.9-5.el7_2 for package: openscap-scanner-1.2.9-5.el7_2.x86_64
--> Processing Dependency: libxslt.so.1()(64bit) for package: openscap-scanner-1.2.9-5.el7_2.x86_64
--> Processing Dependency: libopenscap.so.8()(64bit) for package: openscap-scanner-1.2.9-5.el7_2.x86_64
--> Processing Dependency: libexslt.so.0()(64bit) for package: openscap-scanner-1.2.9-5.el7_2.x86_64
---> Package xml-common.noarch 0:0.6.3-39.el7 will be installed
--> Running transaction check
---> Package libxslt.x86_64 0:1.1.28-5.el7 will be installed
---> Package openscap.x86_64 0:1.2.9-5.el7_2 will be installed
--> Finished Dependency Resolution

Dependencies Resolved

================================================================================================================================
 Package                            Arch                  Version                                  Repository              Size
================================================================================================================================
Installing for group install "Security Tools":
 scap-security-guide                noarch                0.1.25-3.el7.centos.0.1                  os                     672 k
Installing for dependencies:
 libxslt                            x86_64                1.1.28-5.el7                             os                     242 k
 openscap                           x86_64                1.2.9-5.el7_2                            updates                3.5 M
 openscap-scanner                   x86_64                1.2.9-5.el7_2                            updates                 49 k
 xml-common                         noarch                0.6.3-39.el7                             os                      26 k

Transaction Summary
================================================================================================================================
Install  1 Package (+4 Dependent packages)

Total download size: 4.4 M
Installed size: 67 M
Downloading packages:
(1/5): libxslt-1.1.28-5.el7.x86_64.rpm                                                                   | 242 kB  00:00:00     
(2/5): openscap-scanner-1.2.9-5.el7_2.x86_64.rpm                                                         |  49 kB  00:00:00     
(3/5): xml-common-0.6.3-39.el7.noarch.rpm                                                                |  26 kB  00:00:00     
(4/5): scap-security-guide-0.1.25-3.el7.centos.0.1.noarch.rpm                                            | 672 kB  00:00:00     
(5/5): openscap-1.2.9-5.el7_2.x86_64.rpm                                                                 | 3.5 MB  00:00:00     
--------------------------------------------------------------------------------------------------------------------------------
Total                                                                                           5.7 MB/s | 4.4 MB  00:00:00     
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  Installing : libxslt-1.1.28-5.el7.x86_64                                                                                  1/5 
  Installing : openscap-1.2.9-5.el7_2.x86_64                                                                                2/5 
  Installing : openscap-scanner-1.2.9-5.el7_2.x86_64                                                                        3/5 
  Installing : xml-common-0.6.3-39.el7.noarch                                                                               4/5 
  Installing : scap-security-guide-0.1.25-3.el7.centos.0.1.noarch                                                           5/5 
  Verifying  : libxslt-1.1.28-5.el7.x86_64                                                                                  1/5 
  Verifying  : openscap-1.2.9-5.el7_2.x86_64                                                                                2/5 
  Verifying  : openscap-scanner-1.2.9-5.el7_2.x86_64                                                                        3/5 
  Verifying  : xml-common-0.6.3-39.el7.noarch                                                                               4/5 
  Verifying  : scap-security-guide-0.1.25-3.el7.centos.0.1.noarch                                                           5/5 

Installed:
  scap-security-guide.noarch 0:0.1.25-3.el7.centos.0.1                                                                          

Dependency Installed:
  libxslt.x86_64 0:1.1.28-5.el7           openscap.x86_64 0:1.2.9-5.el7_2        openscap-scanner.x86_64 0:1.2.9-5.el7_2       
  xml-common.noarch 0:0.6.3-39.el7       

Complete!
```
## 源码编译安装软件包
>介绍如何使用源代码的形式安装软件包
>主要是以下几个步骤

### 解压源码包
tar  -xzvf  源代码包
[解析]
x为解包、z为解压（仅适合gzip，j bz2则先解包再bunzip解压）、v显示过程、f指定被解压包名
### 配置
进入解压后的目录，用 ./configure [--prefix=/usr/local/filname] 来配置  
这个过程主要是收集系统信息、设置安装目录等（卸载的时候只需将这个目录删除则可）
### 编译
make -j 4  
使用4线程进行编译
### 安装
make install
## 编译安装nginx
> 用编译的方式安装nginx

### 下载最新版源码
**下载nginx最新版源码**
```
[root@VM_200_13_centos ~]# wget http://nginx.org/download/nginx-1.11.3.tar.gz
--2016-08-15 01:07:57--  http://nginx.org/download/nginx-1.11.3.tar.gz
Resolving nginx.org (nginx.org)... 95.211.80.227, 206.251.255.63, 2001:1af8:4060:a004:21::e3
Connecting to nginx.org (nginx.org)|95.211.80.227|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 938045 (916K) [application/octet-stream]
Saving to: 'nginx-1.11.3.tar.gz'

100%[======================================================================================>] 938,045      660KB/s   in 1.4s   

2016-08-15 01:07:59 (660 KB/s) - 'nginx-1.11.3.tar.gz' saved [938045/938045]
```
### 添加运行用户
**为nginx添加运行用户**
```
[root@VM_200_13_centos ~]# useradd -s /sbin/nologin -M nginx
```
-M 不创建宿主目录
-s  指定登陆的shell环境  /sbin/nologin  禁止登陆
### 安装依赖包
使用下面的两条命令安装nginx依赖包，否则编译安装时会出错
```
[root@VM_200_13_centos ~]# yum -y install pcre-devel
[root@VM_200_13_centos ~]# yum -y install zlib-devel
```
### 解压
```
[root@VM_200_13_centos ~]# tar -zxf nginx-1.11.3.tar.gz -C /usr/src/
```
### 配置
```
[root@VM_200_13_centos ~]# cd /usr/src/nginx-1.11.3/
[root@VM_200_13_centos nginx-1.11.3]# ./configure --prefix=/usr/local/nginx --user=nginx --group=nginx
```
输出信息省略
**参数解释**
--prefix指定安装的位置
--user指定运行的用户
--group指定运行的组
### 编译
使用4线程编译
```
[root@VM_200_13_centos nginx-1.11.3]# make -j 4
```
echo $? 输出的结果为0就表示编译成功
```
[root@VM_200_13_centos nginx-1.11.3]# echo $?
0
```
### 安装
使用make install进行安装
```
[root@VM_200_13_centos nginx-1.11.3]# make install
```
输出信息省略
### 优化启动脚本
```
[root@VM_200_13_centos nginx-1.11.3]# cp /usr/local/nginx/sbin/nginx  /usr/sbin/
```
### 启动并验证
```
[root@VM_200_13_centos nginx-1.11.3]# nginx    
[root@VM_200_13_centos nginx-1.11.3]# netstat -anlpt | grep nginx
tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN      22156/nginx: master 
```

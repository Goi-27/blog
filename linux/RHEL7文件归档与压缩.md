> 本文介绍RHEL7.2文件的归档和压缩

## 文件归档
>归档的好处：方便使用、查询、阅读，易于管理 (批量删除文件)

### 常用操作
命令：tar  
作用：将许多文件一起保存至一个单独的磁带或磁盘归档，并能从归档中单独还原所需文件  
用法: tar [选项...] [FILE]...  

| 选项 | 说明 |
| :------------- | :------------- |
| -c | 创建一个新归档 |
| -C | 指定路径归档或解档 |
| -f | 归档文件 |
| -x | 从归档中解出文件 |
| -t | 列出归档内容 |
| -d | 找出归档和文件系统的差异 |
| -v | 详细地列出处理的文件 |
**将文件归档至grub.tar**
```
[root@localhost ~]# tar -cvf  grub.tar  httpd.conf  install.log install.log.syslog
httpd.conf
install.log
install.log.syslog
```
**查看grub.tar归档中的文件**
```
[root@localhost ~]# tar -tvf grub.tar
-rw-r--r-- root/root         0 2015-10-15 15:32 httpd.conf
-rw-r--r-- root/root     47316 2015-10-14 00:28 install.log
-rw-r--r-- root/root     10733 2015-10-14 00:26 install.log.syslog
```
**解档grub.tar至test目录**
```
[root@localhost ~]# mkdir test
[root@localhost ~]# tar -xvf grub.tar  -C test
httpd.conf
install.log
install.log.syslog
[root@localhost ~]# ll test/
total 60
-rw-r--r-- 1 root root     0 10月 15 2015 httpd.conf
-rw-r--r-- 1 root root 47316 10月 14 2015 install.log
-rw-r--r-- 1 root root 10733 10月 14 2015 install.log.syslog
```
### 文件类型
>linux对于文件的扩展名没有像windows要求的那么严格，所以在使用linux的过程中经常会遇到有些文件根本就没有扩展名，哪么我们应该如何去判断没有扩展名的文件，到底是文件还是目录呢？

命令：file
作用：确定文件类型
语法：file [选项...] [文件...]
| 选项 |说明 |
| :------------- | :------------- |
| -b | 列出文件辨识结果时，不显示文件名称 |
| -c | 详细显示指令执行过程，便于排错或分析程序执行的情形 |
| -f | 列出文件中文件名的文件类型 |
| -F | 使用指定分隔符号替换输出文件名后的默认的“：”分隔符 |
| -i | 输出mime类型的字符串 |
| -L | 查看对应软链接对应文件的文件类型 |
| -z | 尝试去解读压缩文件的内容 |
```
[root@localhost ~]# touch a.txt
[root@localhost ~]# file a.txt
a.txt: empty
[root@localhost ~]# file test/
test/: directory
[root@localhost ~]# file /etc/init.d/network
/etc/init.d/network: Bourne-Again shell script text executable
```
![file命令结果](http://img.blog.csdn.net/20160815180514560)
### 大小比对
>将文件进行归档后的归档文件大小为所有文件大小之和

命令：du  
作用：计算每个文件的磁盘用量，目录则取总用量  
语法：du [选项]... [文件]...  

| 选项 | 说明 |
| :------------- | :------------- |
| -a | 显示目录中个别文件的大小 |
| -b | 显示目录或文件大小时以byte为单位 |
| -c | 除了显示个别目录或文件的大小外，同时也显示所有目录或文件的总和 |
| -k | 以KB即1024bytes为单位输出 |
| -m | 以MB为单位输出 |
| -s | 仅显示总计，只列出最后加总的值 |
| -h | 以K，M，G为单位，提高信息的可读性 |
| -x | 以一开始处理时的文件系统为准，若遇上其它不同的文件系统目录则略过 |
| -L<符号链接> | 显示选项中所指定符号链接的源文件大小 |  
| -S | 显示个别目录的大小时，并不含其子目录的大小 |
| -X<文件> | 在<文件>指定目录或文件 |    
| -D | 显示指定符号链接的源文件大小 |  
| -H | 与-h参数相同，但是K，M，G是以1000为换算单位 |   
| -l | 重复计算硬件链接的文件 |

```
[root@localhost ~]# mkdir grub
[root@localhost ~]# tar -cvf grub.tar httpd.conf  install.log install.log.syslog
httpd.conf
install.log
install.log.syslog
[root@localhost ~]# tar -xvf grub.tar  -C grub
httpd.conf
install.log
install.log.syslog
[root@localhost ~]# du -sh grub/*
0       grub/httpd.conf
48K     grub/install.log
12K     grub/install.log.syslog
[root@localhost ~]# ll -sh grub.tar
60K -rw-r--r-- 1 root root 60K 8月  15 16:24 grub.tar
```
## 文件压缩
>压缩文件能节约硬盘的资源和加快文件传输时的速率

压缩格式：xz  
压缩文件格式：.xz  
压缩语法：tar Jcvf filename.xz  SOURCE  
解压语法：tar Jxvf filename.xz <-C 目标解压目录>
```
[root@localhost archive]# tar -Jcvf archive.xz httpd.conf  install.log 
httpd.conf
install.log
[root@localhost archive]# tar -Jxvf archive.xz 
httpd.conf
install.log
```
压缩格式：bzip2  
压缩文件格式：.tar.bz2  
压缩语法：tar jcvf filename.tar.bz2  SOURCE  
解压语法：tar jxvf filename.tar.bz2 <-C 目标解压目录>
```
[root@localhost archive]# tar -jcvf archive.tar.bz2 httpd.conf  install.log 
httpd.conf
install.log
[root@localhost archive]# tar -jxvf archive.tar.bz2
httpd.conf
install.log
```
压缩格式：gzip2  
压缩文件格式：.tar.gz或tgz  
压缩语法：tar zcvf filename.tgz SOURCE  
解压语法：tar zxvf filename.tgz <-C 目标解压目录>
```
[root@localhost archive]# tar -zcvf archive.tgz httpd.conf  install.log 
httpd.conf
install.log
[root@localhost archive]# tar -zxvf archive.tgz 
httpd.conf
install.log
```
压缩格式：zip  
压缩文件格式：.zip  
压缩语法：zip -r filename.zip  SOURCE  
解压语法：unzip <-d 目标解压目录> filename.zip
```
[root@localhost archive]# zip -r archive.zip httpd.conf  install.log 
  adding: httpd.conf (stored 0%)
  adding: install.log (deflated 75%)
[root@localhost archive]# unzip archive.zip 
Archive:  archive.zip
replace httpd.conf? [y]es, [n]o, [A]ll, [N]one, [r]ename: y
 extracting: httpd.conf              
replace install.log? [y]es, [n]o, [A]ll, [N]one, [r]ename: y
  inflating: install.log 
```

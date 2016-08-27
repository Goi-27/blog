## 文件描述符
> 可以理解为linux跟踪打开文件，而分配的一个数字，这个数字有点类似c语言操作文件时候的句柄，通过句柄就可以实现文件的读写操作
用户可以自定义文件描述符范围是：3-num,这个最大数字，跟用户的：ulimit –n 定义数字有关系，不能超过最大值

**查看系统文件描述符最大值**
```
[root@localhost ~]# ulimit -n
1024
```
**linux系统默认文件描述符**

| 文件描述符 | 用途 | POSIX名称 | stdio流 | 说明 |
| :------------- | :------------- | :------------- | :------------- | :------------- |
| 0 | 标准输入 | STDIN_FILENO | stdin | 默认的设备是键盘<br>命令将从标准输入文件中读取<br>在执行过程中的需要的输入数据<br>数据来源于文件|
| 1 | 标准输出 | STDOUT_FILENO | stdout | 默认的设备是显示器<br>命令执行后的输出结果发送到标准输出文件<br>结果输出到文件|
| 2 | 标准错误 | STDERR_FILENO | stderr | 默认的设备是显示器<br>命令将执行期间的各种错误信息发送到标准错误文件<br>错误信息发送到文件|
**标准输入,标准输出和标准错误默认使用键盘和显示器作为关联设备与操作系统进行交互完成最基本的输入,输出操作**
## 命令执行过程
![命令执行过程](http://img.blog.csdn.net/20160819004454990)
## 重定向输出
语法：命令 [文件描述符] >或>> 文件或文件操作符或设备
**查看当前目录下的文件**
```
[root@VM_200_13_centos ~]# ls
test.txt
```
**查看当前目录下的test.txt文件和一个不存在的test1.txt文件**
```
[root@VM_200_13_centos ~]# ls test.txt test1.txt
ls: cannot access test1.txt: No such file or directory
test.txt
```
**将正确的输出重定向到文件success.txt中**
```
[root@VM_200_13_centos ~]# ls test.txt test1.txt 1>success.txt
ls: cannot access test1.txt: No such file or directory
[root@VM_200_13_centos ~]# cat success.txt
test.txt
```
**将错误的输出重定向到文件error.txt中**
```
[root@VM_200_13_centos ~]# ls test.txt  test1.txt 2>error.txt
test.txt
[root@VM_200_13_centos ~]# cat error.txt
ls: cannot access test1.txt: No such file or directory
```
&1：代绑定到表标准输出
&2：代表绑定到标准错误输出
&-：代表关闭与他绑定的文件描述符
**关闭标准错误输出**
```
[root@VM_200_13_centos ~]# ls test.txt  test1.txt >success.txt 2>&-
[root@VM_200_13_centos ~]# cat success.txt
test.txt
```
**将标准错误输出绑定到标准输出**
```
[root@VM_200_13_centos ~]# ls test.txt  test1.txt > all.txt 2>&1
[root@VM_200_13_centos ~]# cat all.txt
ls: cannot access test1.txt: No such file or directory
test.txt
```
**将标准错误输出重定向到黑洞设备**
```
[root@VM_200_13_centos ~]# ls test.txt  test1.txt 2>/dev/null
test.txt
```
**&>：混合输出（不分对错）**
```
[root@VM_200_13_centos ~]# ls test.txt  test1.txt &> all.txt
[root@VM_200_13_centos ~]# cat all.txt
ls: cannot access test1.txt: No such file or directory
test.txt
```
**注意事项：**
1. shell遇到”>”操作符，会判断右边文件是否存在，如果存在就先删除，并且创建新文件。不存在直接创建。 无论左边命令执行是否成功。右边文件都会变为空
2. “>>”操作符，判断右边文件，如果不存在，先创建
以添加方式打开文件，会分配一个文件描述符[不特别指定，默认为1,2]然后，与左边的标准输出（1）或错误输出（2） 绑定
3. 当命令：执行完，绑定文件的描述符也自动失效。0,1,2又会空闲
4. 一条命令启动，命令的输入，正确输出，错误输出，默认分别绑定0,1,2文件描述符
5. 一条命令在执行前，先会检查输出是否正确，如果输出设备错误，将不会进行命令执行

## 重定向输入
>将命令中接收输入的途径由默认的键盘改为其他文件而不是等待从键盘输入
通过重定向输入可以使一些交互式操作过程能够通过读取文件来完成

操作符: <
使用passwd设置密码时,每次都根据提示输入密码比较烦琐
改用重定向输入将可以忽略交互式的过程而自动完成密码设置
使用非交互式的去执行设置密码
```
[root@VM_200_13_centos ~]# touch passwd.txt
[root@VM_200_13_centos ~]# echo 123456 > passwd.txt
[root@VM_200_13_centos ~]# passwd test --stdin < passwd.txt
Changing password for user test.
passwd: all authentication tokens updated successfully.
```
## 管道
>管道命令操作符是：”|”,它仅能处理经由前面一个指令传出的正确输出信息
也就是 standard output 的信息，对于 stdandard error 信息没有直接处理能力
然后，传递给下一个命令，作为标准的输入 standard input

![管道](http://img.blog.csdn.net/20160819004346552)
command1正确输出，作为command2的输入 然后comand2的输出作为，comand3的输入 ，comand3输出就会直接显示在屏幕上面了
通过管道之后：comand1,comand2的正确输出不显示在屏幕上面
1. 管道命令只处理前一个命令正确输出，不处理错误输出
2. 管道命令右边命令，必须能够接收标准输入流命令才行

```
[root@VM_200_13_centos ~]# ls -f | grep all
all.txt
```
## 管道与重定向的区别
+ 左边的命令应该有标准输出 | 右边的命令应该接受标准输入
+ 左边的命令应该有标准输出 > 右边只能是文件
+ 左边的命令应该需要标准输入 < 右边只能是文件
+ 管道触发两个子进程执行"|"两边的程序,而重定向是在一个进程内执行

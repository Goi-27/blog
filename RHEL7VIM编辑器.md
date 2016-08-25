> 本文介绍Vim编辑器的使用

## vi和vim的区别
它们都是多模式编辑器 不同的是vim是vi的升级版本
它不仅兼容vi的所有指令而且还有一些新的特性在里面
vim的这些优势主要体现在以下几个方面
+ **多级撤消** 在vi里 按u只能撤消上次命令 而在vim里可以无限制的撤消
+ **易用性** vi只能运行于unix中 而vim不仅可以运行于unix windows mac等多操作平台
+ **语法加亮** vim可以用不同的颜色来加亮你的代码
+ **可视化操作** vim不仅可以在终端运行 也可以运行于x window  mac windows
+ **完全兼容vi** 某些情况下 你可以把vim当成vi来使用

## vim主要模式介绍
**确认是否安装了vim工具**
```
[root@VM_200_13_centos /]# rpm -qf `which vim`
vim-enhanced-7.4.160-1.el7.x86_64
```
**如果没有安装使用yum在线安装即可**
```
[root@VM_200_13_centos /]# yum -y install vim
```
## 三种模式
**vim编辑器三种模式：命令模式、插入模式、末行模式**
![三种模式](http://img.blog.csdn.net/20160813134804129)
> 从上图可以看出：末行模式切换到插入模式需要先切换到命令模式

## 按键说明
### 光标移动
| 按键     | 说明     |
| :------------- | :------------- |
| h或← | 光标向左移动一个字符 |
| j或↓ | 光标向下移动一个字符 |
| k或↑ | 光标向上移动一个字符 |
| l或→ | 光标向右移动一个字符 |
| Ctrl+f或PageDown | 向下翻一页 |
| Ctrl+b或PageUp | 向上翻一页 |
| + | 光标移动到非空白字符的下一列 |
| - | 光标移动到非空白字符的上一列|
| n+space| 光标移动到本行第n个字符处 |
| 0或home | 光标所在行行首 |
| $或end | 光标所在行行尾 |
| H | 移动光标到屏幕第一行行首 |
| M | 移动光标到屏幕中央那一行的行首 |
| L | 移动光标到屏幕最后一行行首 |
| G | 移动光标到当前文件的末尾 |
| n+G | 移动光标到这个文件的第n行 |
| gg | 移动光标到这个文件的第一列 相当于1+G |
| :n | 移动光标到这到这个文件的第n行 相当于nG |
| n+Enter | 向下移动n行 |

### 查找
| 按键     | 说明     |
| :------------- | :------------- |
| /word | 向光标之下寻找一个名称为word的字符串 |
| ?word | 想光标之上寻找一个名称为word的字符串 |
| n | 重复前一个寻找动作<br>如果执行/vim去向下寻找vim字符串<br>按n会向下继续寻找下一个vim字符串<br>如果执行?vim按n则会向上寻找vim字符串|
| N | 与n相反，进行反向搜索 |
| :n1,n2s/word1/word2/g | 在n1与n2行之间寻找word1字符串并替换为word2字符串|
| :1,$s/word1/word2/gc | 从首行到末行寻找word1字符串并替换为word2字符串<br>并且在取代前提示是否确认替换 |
| :% s/a/b | 将每行的第一个a替换为b |
| :% s/a/b/g | 将所有的a替换为b |
> % 表示所有内容
> g 全部
> c confirm提示确认

### 复制粘贴删除
| 按键 | 说明 |
| :------------- | :------------- |
| x | 向后删除一个字符 |
| X | 向前删除一个字符 |
| BackSpace | 向前删除一个字符 |
| nx | 连续向后删除n个字符 |
| dd | 删除光标所在行 |
| ndd | 删除包含光标所在行在内的向下n行 |
| d1G | 删除首行到光标所在行的所有数据 |
| dG | 删除光标所在行到最后一行的所有数据 |
| d$ | 删除光标所在行光标位置到该行行尾的所有数据 |
| d0 | 删除光标所在行光标位置到该行行首的所有数据 |
| yy | 复制光标所在的整行 |
| nyy | 复制光标所在的向下n行 |
| y1G | 复制首行到光标所在行的所有数据 |
| yG | 复制光标所在行到末行的所有数据 |
| y0 | 复制光标所在位置到光标所在行行首的所有数据 |
| y$ | 复制光标所在位置到光标所在行行尾的所有数据 |
| p | 将已复制的数据粘贴到光标下一行 |
| P | 将已复制的数据粘贴到光标上一行 |
| J | 将光标所在行与下一行的数据合并为一行 |
| c | 重复删除多个数据 |
| u | 撤销 |
| Ctrl+r | 反撤销 |
| . | 重复前一个动作 |
### 插入和修改
| 按键 | 说明     |
| :------------- | :------------- |
| i | 从光标所在位置插入 |
| I | 从光标所在行第一个非空字符位置插入 |
| a | 从光标所在的下一个字符位置插入 |
| A | 从光标所在行的最后一个字符处开始插入 |
| o | 从光标所在位置向下插入新的一行 |
| O | 从光标所在位置向上插入新的一行 |
| r | 替换光标所在位置的一个字符 |
| R | 替换光标所在位置的所有字符直到按ESC结束 |
| ESC | 退出插入模式回到命令模式 |
### 末行模式
> 在命令模式下按:键会进入到末行模式

| 按键 | 说明 |
| :------------- | :------------- |
| :w | 保存数据写入到硬盘 |
| :W | 强制写入数据到只读文件（需要有对该文件的写入权限）|
| :q | 退出vim |
| :q! | 强制退出不保存 |
| :wq | 保存后退出 |
| :wq! | 强制保存退出 |
| ZZ | 如果文件没有更改则不保存并退出如何有更改则保存退出 |
| :w test.txt | 将更改后的数据另存为test.txt文件 |
| :r test.txt | 将test.txt这个文件的内容追加到光标所在行之后 |
| :n1,n2 w test.txt | 将n1到n2行之间的内容存储到test.txt文件中 |
| :! command | 暂时离开vim到系统文本模式下查看执行command执行的结果 |
| :set nu       | 显示行号 |
| :set nonu       | 撤销行号 |
### 区块选择
| 按键 | 说明 |
| :------------- | :------------- |
| v | 将光标经过的地方全部选中 |
| V | 将光标经过的行全部选中 |
| Ctrl+v | 以长方形的方式选择数据 |
| y | 复制未被选中的地方 |
| d | 删除未被选中的地方 |
| p | 将复制的内容粘贴在光标所在位置 |
## vim警告信息
当我们在使用vim进行编辑时，vim会在与被编辑的文件的目录下再创建一个名为.filename.swp的文件  
当你因为某些原因（系统断线、Ctrl+z等）而没有保存你正在编辑的文件时，这个swp文件就能发挥救援功能

**创建test.txt文件后使用vim编辑再按Ctrl+z模拟非正常保存退出**
```
[root@VM_200_13_centos ~]# touch test.txt 
[root@VM_200_13_centos ~]# vim test.txt 

[7]+  Stopped                 vim test.txt
```
**再次使用vim编辑test.txt文件出现以下信息**
```
E325: ATTENTION
Found a swap file by the name ".test.txt.swp"
          owned by: root   dated: Sat Aug 13 11:42:16 2016
         file name: ~root/test.txt
          modified: no
         user name: root   host name: VM_200_13_centos
        process ID: 20247 (still running)
While opening file "test.txt"
             dated: Sat Aug 13 11:38:53 2016

(1) Another program may be editing the same file.  If this is the case,
    be careful not to end up with two different instances of the same
    file when making changes.  Quit, or continue with caution.
(2) An edit session for this file crashed.
    If this is the case, use ":recover" or "vim -r test.txt"
    to recover the changes (see ":help recovery").
    If you did this already, delete the swap file ".test.txt.swp"
    to avoid this message.

Swap file ".test.txt.swp" already exists!
[O]pen Read-Only, (E)dit anyway, (R)ecover, (Q)uit, (A)bort:
```
**上面的信息已经明确的说明了原因**

1. 其他人或程序同时在修改这个文件  
由于Linux是多用户多任务的环境，因此很可能有很多人同时在编辑同一个文件。  
如果在多人共同编辑的情况下，大家同时储存，这个文件会被修改的面目全非。  
这是出现这个警告的原因之一，解决方案：  

  + 找到正在修改这个文件的程序或用户，请他结束该vim的工作后再进行处理
  + 如果只是查看文件的内容而不进行编辑行为，按O以只读模式打开
  
2. 在上一次的vim编辑时异常中断
比如使用SSH远程连接服务器使用vim进行编辑时连接断开这种情况。  
根据不同的情况采用不同的解决方案：  

  + 如果之前的vim操作没有保存，按R进入恢复模式就可以找回之前为存储的数据
  + 如果确定之前的vim操作是无用的，按D删除之前swp文件，此时vim会创建新的swp文件

**警告信息中的六个按键的作用**

| 按键 | 说明 |
| :------------- | :------------- |
| O | 以只读方式打开文件 |
| E | 以正常方式编辑文件并且不会载入之前的swp文件中的内容<br>（谨慎操作：有可能会造成多个使用者互相修改文件的情况） |
| R | 恢复之前未存储的内容 |
| D | 删除之前的swp文件，此时vim会创建新的swp文件 |
| Q | 退出vim |
| A | 跟Q差不多，忽略这次的编辑行为返回到文本界面 |
## 多文件编辑
vim可以同时编辑多个文件
```
[root@VM_200_13_centos ~]# vim anaconda-ks.cfg  test.txt
```
| 按键 | 说明 |
| :------------- | :------------- |
| :n | 编辑上一个文件 |
| :N | 编辑下一个文件 |
| :files | 列出本次vim指令打开的所有文件 |
```
:files
  1 %a   "anaconda-ks.cfg"              line 1
  2      "test.txt"                     line 0
```
## 多窗口功能
vim可以分屏编辑多个文件

| 按键 | 说明 |
| :------------- | :------------- |
| :sp [文件名] | 如果有加文件名则以上下分屏的模式在右边打开一个新的文件<br>否则在下面的窗口打开当前文件<br>使用vim的-o参数打开多个文件时会上下分屏,使用-O参数会左右分屏 |
| Ctrl+w+j或Ctrl+w+↓ | 先按下Ctrl+w，再按下j或↓在上下两个窗口间进行窗口切换<br>左右分屏的情况使用 ←→或hl左右切换窗口|
| Ctrl+w+q | 退出关闭光标所在的窗口 |
## 补全功能
vim和大部分集成开发环境一样包含补全功能

| 按键 | 说明     |
| :------------- | :------------- |
| [ctrl]+x -> [ctrl]+n | 通过目前正在编辑的这个“文件的内容文字”作为关键字，予以补齐 |
| [ctrl]+x -> [ctrl]+f | 以当前目录内的“文件名”作为关键字，予以补齐 |
| [ctrl]+x -> [ctrl]+o | 以扩展名作为语法补充，以vim内置的关键字，予以补齐 |
## 常用指令示意图
![常用指令示意图](http://img.blog.csdn.net/20160813135126381)
## 乱码处理
造成乱码的原因是文件的源编码和vim的编码不一致  
比如：在windows中编辑好的汉字文本文档上传到Linux打开就乱码了  
解决方案：使用iconv工具解决乱码  
**查看是否安装了iconv，如果没有安装使用yum或rpm安装即可**
```
[root@VM_200_13_centos ~]# rpm -qf `which iconv`
glibc-common-2.17-106.el7_2.4.x86_64
```
**使用iconv命令转码**  
输入/输出格式规范：  
-f, --from-code=名称 原始文本编码  
-o, --output=FILE 输出文件  
-l, --list 列举所有已知的字符集  
iconv -f 原本编码 -t 新编码 filename [-o newfile]
例：
```
[root@VM_200_13_centos ~]# iconv -f gb2312 test.txt -o new.txt
```
## 附录
### vim小抄
![vim小抄](http://img.blog.csdn.net/20160815145542882)
### vim键盘图
![vim键盘](http://img.blog.csdn.net/20160815145623680)


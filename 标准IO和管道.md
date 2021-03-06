# 标准输入和输出  
* 程序：指令+数据  或者说： 数据结构+算法  
程序必须能够读入输入并进行加工产生结果：程序获得结果的数据可以是变量、数组、列表、文件等；程序输出的的结果也可以是变量、数组、列表、文件等。  
读入数据：input  
输出数据：output
* 打开的文件都有一个fd: file descriptor (文件描述符)  
* Linux给程序提供三种I/O设备  
标准输入（STDIN）－0默认接受来自键盘的输入vv
标准输出（STDOUT）－1默认输出到终端窗口  
标准错误（STDERR）－2默认输出到终端窗口  
I/  

* I/O重定向：改变默认位置   
## 把输入和错误重定向到文件  
* STDOUT和STDERR可以被重定向到 文件 、 
命令、操作符号、文件名  
支持的操作符号包括：  
\> 把STDOUT重定向到文件  
2>把STDERR重定向到文件  
&>把所有输出重定向到文件

* \> 文件内容会被覆盖
set –C 禁止将内容覆盖已有文件,但可追加  
\>| file 强制覆盖  
set +C 允许覆盖

* \>> 原有内容基础上，追加内容  
2>覆盖重定向错误输出数据流  
2>> 追加重定向错误输出数据流

* 标准输出和错误输出各自定向至不同位置  
COMMAND > /path/to/file.out2>   /path/to/error.out 

* 合并标准输出和错误输出为同一个数据流进行重  定向
&>覆盖重定向  
&>> 追加重定向  
COMMAND > /path/to/file.out2>&1 （顺序很重要）  
COMMAND >> /path/to/file.out2>&1  
* ()：合并多个程序的STDOUT  
(cal2007;cal2008)> all.txt   

* tr命令  
tr转换和删除字符  
tr[OPTION]... SET1 [SET2]  
选项  ：  
-c–C --complement：取字符集的补集    
-d--delete：删除所有属于第一字符集的字符    
-s--squeeze-repeats：把连续重复的字符以单独一个字符表示    
-t--truncate-set1：将第一个字符集对应字符转化为第二字符集对应的字符    
[:alnum:]：字母和数字  
[:alpha:]：字母  
[:cntrl:]：控制（非打印字符)  
[:digit:]：数字  
[:graph:]:图形字符  
[:lower:]：小写字母   
[:print:]：可打印字符 
[:punct:]：标点符号  
[:space:]：空白字符  
[:upper:]：大写字母  
[:xdigit:]：十六进制字符  

## 从文件中导入STDIN  
* 使用<来重定向标准输入  
* 某些命令能够接受从文件中导入的STDIN  
tr‘a-z’‘A-Z’</etc/issue  
```bash  
[root@Centos7 date]#tr -t 'a-z' 'A-Z'  < /etc/issue
\S
KERNEL \R ON AN \M  
```  
*该命令会把/etc/issue中的小写字符都转换成写写字符  
* tr–d abc< /etc/fstab删除fstab文件中的所有abc中任意字符  
* cat > file  
mage  
wangxiaochun  
按ctrl+d离开，可以使用文件来代替键盘的输入  
* Cat > filea< fileb  
## 把多行发送给STDIN  
* 使用“<<终止词”命令从键盘把多行重导向给STDIN
直到终止词位置的所有文本都发送给STDIN
有时被称为就地文本（heretext）
mail-s"PleaseCall"admin@magedu.com<<END  
\>HiWang,  
\>  
\>Pleasegivemeacallwhenyougetin.Wemayneed  
\>todosomemaintenanceonserver1.  
\>  
\>Detailswhenyou'reon-site  
\>Zhang  
\>END  

# 管道  
* 管道（使用符号“|”表示）用来连接命令
命令1 | 命令2 | 命令3 | …    
将命令1的STDOUT发送给命令2的STDIN，命令2的STDOUT发送到命令3的STDIN  
* STDERR默认不能通过管道转发，可利用2>&1 或|& 实现  
最后一个命令会在当前shell进程的子shell进程中执行用来  
组合多种工具的功能  
ls| tr‘a-z’ ‘A-Z’

* less ：一页一页地查看输入  
ls-l/etc|less  
* mail：通过电子邮件发送输入  
echo"testemail"|mail-s   "test"user@example.com  
* lpr：把输入发送给打印机  
echo"testprint"|lpr-Pprinter_name  

* 管道中-符号  
示例:  
将/home 里面的文件打包，但打包的数据不是记录到文件，而是传送到stdout，经过管道后，将tar -cvf-/home 传送给后面的tar -xvf-, 后面的这个-则是取前一个命令的stdout，因此，就不需要使用临时file了  
tar -cvf-/home | tar -xvf-  

## 重定向到多个目标（tee）  
命令1 | tee[-a ] 文件名| 命令2  
把命令1的STDOUT保存在文件中，做为命令2的输入  
-a 追加

使用：  
保存不同阶段的输出  
复杂管道的故障排除  
同时查看和记录输出  

## 练习  
1、将/etc/issue文件中的内容转换为大写后保存至/tmp/issue.out文件中  
```bash 
  [root@Centos7 date]#tr -t 'a-z' 'A-Z'  < /etc/issue
\S
[root@Centos7 date]#cat /etc/issue |tr -t '[:lower:]' '[:upper:]' >/tmp/issue.out
```  

2、将当前系统登录用户的信息转换为大写后保存至/tmp/who.out文件中  
```bash  
[root@Centos7 date]#who | tr -t '[:lower:]' '[:upper:]' >/tmp/who.out 
```  
3、一个linux用户给root发邮件，要求邮件标题为”help”，邮件正文如下：Hello, I am 用户名,The system version is here,pleasehelp me to check it ,thanks!
操作系统版本信息   
  ```bash  
[root@Centos7 date]#mail -s "help"  root@edu.com << EOF  
```
4、将/root/下文件列表，显示成一行，并文件名之间用空格隔开  
```bash
[root@Centos7 ~]#ls -f |tr -t '\n' '

```  
5、计算1+2+3+..+99+100的总和  
```bash  
[root@Centos7 ~]#echo {1..100} | tr -t ' ' '+' | bc  
```
6、删除Windows文本文件中的‘^M’字符  
```bash  
[root@Centos7 ~]#cat Windows |tr -cd 'M'  
```  
7、处理字符串“xt.,l 1 jr#!$mn2 c*/fe3 uz4”，只保留其中的数字和空格  
```bash 
[root@Centos7 ~]#echo 'xt.,l 1 jr#!$mn 2 c*/fe 3 uz 4' | tr -cd '[:digit:][:space:]'  
```
8、将PATH变量每个目录显示在独立的一行  
```bash  
[root@Centos7 ~]#echo $PATH | tr -t ':'  '\n'
```  
10、将文件/etc/centos-release中每个单词（由字母组成）显示在独立一行，并无空行
```bash
[root@Centos7 ~]#cat /etc/centos-release | tr -cd '[:alpha:] '|tr -s  ' ' |tr -t ' ' '\n'
```

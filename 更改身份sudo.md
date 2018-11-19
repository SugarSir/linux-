* 更改身份  
su 切换身份:su –l username –c 'command' 
sudo  
来自sudo包，man 5 sudoers  
sudo能够授权指定用户在指定主机上运行某些命令。如果未授权用户尝试使用 sudo，会提示联系管理员  
sudo可以提供日志，记录每个用户使用sudo操作  
sudo为系统管理员提供配置文件，允许系统管理员集中地管理用户的使用权限和使用的主机  
sudo使用时间戳文件来完成类似“检票”的系统，默认存活期为5分钟的“入场券”  
通过visudo命令编辑配置文件，具有语法检查功能  
visudo –c 检查语法   
visudo -f /etc/sudoers.d/test   
export  EDITOR=vim  声明变量  用vim 打开


* sudo
配置文件：/etc/sudoers, /etc/sudoers.d/  
时间戳文件：/var/db/sudo  
日志文件：/var/log/secure  
配置文件支持使用通配符glob  
？:任意单一字符  
\*：匹配任意长度字符  
[wxc]:匹配其中一个字符  
[!wxc]:除了这三个字符的其它字符  
\x : 转义  
[[alpha]] :字母 示例： /bin/ls [[alpha]]*  
配置文件规则有两类  
1、别名定义:不是必须的  
2、授权规则:必须的    
实验1：授权tang 挂光盘   
```bash   
[root@Centos7 aide]#vim /etc/sudo  
root    ALL=(ALL)       ALL  
tang    ALL=(root)      /usr/bin/mount   /dev/sr0 /mnt/  
```  
```bash  
[root@Centos7 aide]#su - tang  
```  
```bash
[tang@Centos7 ~]$sudo mount /dev/sr0 /mnt/  

[sudo] password for tang: 
mount: /dev/sr0 is write-protected, mounting read-only
```
5分钟检查一次输入口令  

* sudoers  
授权规则格式：  
用户 登入主机=(代表用户) 命令  
示例：  
root ALL=(ALL) ALL  
格式说明：  
user: 运行命令者的身份  
host: 通过哪些主机  
(runas)：以哪个用户的身份  
command: 运行哪些命令

tang  192.168.228.183=ALL  ALL
授权tang 用户可以修改文件  
tang  ALL=sudoedit  

sudoedit /etc/sudoers  通过这种方式修改文件

### 别名  
* Users和runas:  
username    
#uid  
%group_name
%#gid
user_alias|runas_alias

* host:
ip或hostname
network(/netmask)
host_alias

* command:
command name
directory
sudoedit
Cmnd_Alias    

别名书写格式  
User_Alias  ADMIN=tang,mage   
Cmnd_Alias  DISKCMD=/bin/fdisk,/bin/mkfs  
Host_Alias  LOCALNET=192.168.228.0/24  
Runas_Alias ADDMIN=root    
ADMIN   LOCALNET=(ADDMIN)   DISKCMD  

* sudo别名和示例  
别名有四种类型：User_Alias, Runas_Alias, Host_Alias ，Cmnd_Alias   
别名格式：[A-Z]([A-Z][0-9]_)*  
别名定义：  
Alias_Type NAME1 = item1, item2, item3 : NAME2 = item4, item5  
示例1：  
Student ALL=(ALL) ALL  
%wheel ALL=(ALL) ALL  
示例2：  
student ALL=(root) /sbin/pidof,/sbin/ifconfig  
%wheel ALL=(ALL) NOPASSWD: ALL   授权用户免输口令


* sudo示例  
示例3  
User_Alias NETADMIN= netuser1,netuser2  
Cmnd_Alias NETCMD = /usr/sbin/ip  
NETADMIN ALL=（root） NETCMD  
示例4  
User_Alias SYSADER=wang,mage,%admins  
User_Alias DISKADER=tom  
Host_Alias SERS=www.magedu.com,172.16.0.0/24  
Runas_Alias OP=root  
Cmnd_Alias SYDCMD=/bin/chown,/bin/chmod  
Cmnd_Alias DSKCMD=/sbin/parted,/sbin/fdisk  
SYSADER SERS= SYDCMD,DSKCMD  
DISKADER ALL=(OP) DSKCMD    


* sudo示例  
示例4  
User_Alias ADMINUSER = adminuser1,adminuser2  
Cmnd_Alias ADMINCMD = /usr/sbin/useradd，/usr/sbin/usermod, /usr/bin/passwd [a-zA-Z]*, !/usr/bin/passwd root   可以修改任何人的口令
除了root
ADMINUSER ALL=(root) NOPASSWD:ADMINCMD，PASSWD:/usr/sbin/userdel  
 示例5  
Defaults:wang runas_default=tom  
wang ALL=(tom,jerry) ALL  wang默认代表tom，只有jia -u jerry 时才代表Jerry  
示例6  
wang 192.168.1.6,192.168.1.8=(root) /usr/sbin/,!/usr/sbin/useradd  wang 能代表root执行/usr/sbin/下所有命令除了 /usr/sbin/useradd

示例7  
wang ALL=(ALL) /bin/cat /var/log/messages*     

wang All=(root)  /bin/cat /var/log/message*,!/var/cat /var/log/message* *

* sudo命令  
ls -l /usr/bin/sudo  
sudo –i –u wang 切换身份  
sudo [-u user] COMMAND  
-V 显示版本信息等配置信息  
-u user 默认为root  
-l,ll 列出用户在主机上可用的和被禁止的命令  
-v 再延长密码有效期限5分钟,更新时间戳  
-k 清除时间戳（1970-01-01），下次需要重新输密码  
-K 与-k类似，还要删除时间戳文件  
-b 在后台执行指令  
-p 改变询问密码的提示符号  
示例：-p "password on %h for user %p:"     



* TCP_Wrappers介绍  
作者：Wieste Venema，IBM，Google  
工作在第四层（传输层）的TCP协议  
对有状态连接的特定服务进行安全检测并实现访问控制  
以库文件形式实现  
某进程是否接受libwrap的控制取决于发起此进程的程序在编译时是否针对libwrap进行编译的  
判断服务程序是否能够由tcp_wrapper进行访问控制的方法：  
ldd /PATH/TO/PROGRAM|grep libwrap.so  
strings PATH/TO/PROGRAM|grep libwrap.so     
tcp_wrapper 能实现一些类似于防火墙的功能（但是他只能基于使用一些libwarap.so 库的服务）

* TCP_Wrappers的使用  
配置文件：/etc/hosts.allow, /etc/hosts.deny    （这两文件改完就生效）
帮助参考：man 5 hosts_access，man 5 hosts_options  
检查顺序：hosts.allow，hosts.deny（默认允许）  
注意：一旦前面规则匹配，直接生效，将不再继续  
基本语法:  
daemon_list@host: client_list [ :options :option… ]  
Daemon_list@host格式  
单个应用程序的二进制文件名，而非服务名，例如vsftpd  
以逗号或空格分隔的应用程序文件名列表，如:sshd,vsftpd  
ALL表示所有接受tcp_wrapper控制的服务程序  
主机有多个IP，可用@hostIP来实现控制  
如：in.telnetd@192.168.0.254    


* TCP_Wrappers的使用  
客户端Client_list格式  
以逗号或空格分隔的客户端列表  
基于IP地址：192.168.10.1 192.168.1.  
基于主机名：www.magedu.com .magedu.com 较少用  
基于网络/掩码：192.168.0.0/255.255.255.0 （centos6） 
基于net/prefixlen: 192.168.1.0/24（CentOS7）  
基于网络组（NIS 域）：@mynetwork  
内置ACL：ALL（不带 . ），LOCAL（本地），KNOWN（能解析），UNKNOWN，（不能把主机和ip做解析）PARANOID  （正向解析和反向解析不一致）
  
* EXCEPT用法： 
示例：  
vsftpd: 172.16. EXCEPT 172.16.100.0/24 EXCEPT 172.16.100.1    
示例  
示例：只允许192.168.1.0/24的主机访问sshd  
/etc/hosts.allow  
sshd: 192.168.1.  
/etc/hosts.deny  
sshd :ALL  
示例：只允许192.168.1.0/24的主机访问telnet和vsftpd服务  
/etc/hosts.allow  
vsftpd,in.telnetd: 192.168.1.  
/etc/host.deny  
vsftpd,in.telnetd: ALL    


* TCP_Wrappers的使用  
[:options]选项：  
帮助：man 5 hosts_options  
deny 主要用在/etc/hosts.allow定义“拒绝”规则  
如：vsftpd: 172.16. :deny  
allow 主要用在/etc/hosts.deny定义“允许”规则  
如：vsftpd:172.16. :allow  
spawn 启动一个外部程序完成执行的操作  
twist 实际动作是拒绝访问,使用指定操作替换当前服务,标准输出和ERROR发送到客户 端,默认至/dev/null  
测试工具：  
tcpdmatch [-d] daemon[@host] client  
-d 测试当前目录下的hosts.allow和hosts.deny    

总结：
httpd 虽然没有用到libwrap 模块 但是xinetd用到了libwrap 模块所以xinetd 间接的起到控制httpd的作用  

示例    
cho "$(date +%%F) login attempt from %c to %s,%d" >>/var/log/sshd.log  
说明：  
在/etc/hosts.allow中添加，允许登录，并记录日志  
在/etc/hosts.deny中添加，拒绝登录，并记录日志  
%c 客户端信息  
%s 服务器端信息  
%d 服务名  
%p 守护进程的PID  
%% 表示%  
vsftpd: 172.16. :twist /bin/echo “connection prohibited”  
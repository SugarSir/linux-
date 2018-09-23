
# 1文件系统与目录结构
## 1.1文件系统结构
- /boot: 引导文件存放目录，内核文件（vmlinuz）、引导加载器（bootloadergrub)都存放于此目录   
例如：
>> ```bash
>>drwxr-xr-x. 2 root root     4096 Sep 19 13:41 grub
>>-rw-r--r--. 1 root root   216063 Jun 20 05:30 symvers-2.6.32-754.el6.x86_64.gz
>>-rw-r--r--. 1 root root  2652834 Jun 20 05:29 System.map-2.6.32-754.el6.x86_64
>>-rwxr-xr-x. 1 root root  4315504 Jun 20 05:29 vmlinuz-2.6.32-754.el6.x86_64
>>```
+ bin: 供所有用户使用的基本命令；不能关联至独立分区，OS启动即会用到的程序  
例如：
>>```bash
>>-rwxr-xr-x. 1 root root 113392 Jun 19 23:15 mv
>>-rwxr-xr-x. 1 root root  50088 Jun 19 23:15 mkdir
>>```

* /sbin: 管理类的基本命令；不能关联至独立分区，OS启动即会用到的程序  
例如：
>>```bash
>>-rwxr-xr-x. 1 root root   16672 Mar 22  2017 rmmod
>>-rwxr-xr-x. 1 root root   14352 Jun 19 23:40 runlevel
>>```
- /lib: 启动时程序依赖的基本共享库文件以及内核模块文件(/lib/modules)  
例如 ：
>>```bash
>>dr-xr-xr-x.  3 root root  4096 Sep 19 13:35 modules
>>drwxr-xr-x.  2 root root  4096 Sep 19 13:35 lsb
>>```
>>
- lib64: 专用于x86_64系统上的辅助共享库文件存放位置
- /etc: 配置文件目录
+ /home: 普通用户家目录
- /root: 管理员的家目录
- /media: 便携式移动设备挂载点
- /mnt: 临时文件系统挂载点
- /dev: 设备文件及特殊文件存储位置
 block device，随机访问
 character device，线性访问
- /opt: 第三方应用程序的安装位置
+ /srv: 系统上运行的服务用到的数据
- /tmp: 临时文件存储位置
- /usr: 
 >>>bin: 保证系统拥有完整功能而提供的应用程序  
 >>>sbin:  
 >>>lin: 32位使用  
 >>>lib64: 只存在64位系统  
 >>>include: C程序的头文件(header files)  
 >>>share: 结构化独立的数据，例如doc, man等  
 >>>local: 第三方应用程序的安装位置
 - /var:
 >>>cache: 应用程序缓存数据目录  
 >>>lib: 应用程序状态信息数据  
 >>>local: 专用于为/usr/local下的应用程序存储可变数据  
 >>>lock:  锁文件  
 >>>log: 日志目录及文件  
 >>>opt: 专用于为/opt下的应用程序存储可变数据  
 >>>run: 运行中的进程相关数据,通常用于存储进程pid文件  
 >>>spool: 应用程序数据池  
 >>>tmp: 保存系统两次重启之间产生的临时数据  
 /proc: 用于输出内核与进程信息相关的虚拟文件系统  
 /sys: 用于输出当前系统上硬件设备相关信息虚拟文件系统  
 /selinux: security enhanced Linux，selinux相关的安全策略等信息的存储位置
 ## LINUX上的应用程序的组成
 * 二进制程序：/bin, /sbin, /usr/bin, /usr/sbin, /usr/local/bin, /usr/local/sbin
 * 库文件：/lib, /lib64, /usr/lib, /usr/lib64, /usr/local/lib, /usr/local/lib64
 * 配置文件：/etc, /etc/DIRECTORY, /usr/local/etc
 * 帮助文件：/usr/share/man, /usr/share/doc, /usr/local/share/man,
/usr/local/share/doc
## Centos 7目录变化
* /bin 和 /usr/bin 
* /sbin 和 /usr/sbin
* /lib 和/usr/lib 
* /lib64 和 /usr/lib64  
如下：
```bash
lrwxrwxrwx. 1 root root 7 Sep 19 20:50 /bin -> usr/bin
lrwxrwxrwx. 1 root root 8 Sep 19 20:50 /sbin -> usr/sbin
lrwxrwxrwx. 1 root root 7 Sep 19 20:50 /lib -> usr/lib
lrwxrwxrwx. 1 root root 9 Sep 19 20:50 /lib64 -> usr/lib64
```  
Centos 7利用连接路径方式/bin ,/sbin, /lib, lib64是连接路径;usr/bin, usr/sbin, usr/lib, usr/lib64是真实路径。
## LINUX下的文件类型  
* -：普通文件
* d: 目录文件
* b: 块设备
* c: 字符设备
* l: : 符号链接文件
* p: 管道文件pipe
* s: 套接字文件sock  
例如：
```bash 
-rw-r--r--.  1 root root       23 Apr 29 00:35 issue
drwxr-xr-x.  6 root root     4096 Sep 19 21:05 sysconfig
brw-rw----. 1 root disk      8,   1 Sep 20 00:54 sda1
crw-rw----. 1 root tty       7,   0 Sep 20 00:54 vcs
lrwxrwxrwx.  1 root root       35 Sep 19 21:04 localtime -> ../usr/share/zoneinfo/Asia/Shanghai
```
### 具体的LINUX系统文件目录也可以参考官网: [www.pathname.con](http://www.pathname.con/fhs)



![End](https://p1.ssl.qhmsg.com/dr/270_500_/t01250aac1744905f28.jpg?size=550x235)







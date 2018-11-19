* PAM认证机制
它提供了对所有服务进行认证的中央机制，适用于本地登录，远程登录，如：telnet,rlogin,fsh,ftp,点对点协议PPP，su等应用程序中，系统管理员通过PAM配置文件来制定不同应用程序的不同认证策略；应用程序开发者通过在服务程序中使用PAM API(pam_xxxx( ))来实现对认证方法的调用；而PAM服务模块的开发者则利用PAM SPI来编写模块（主要调用函数pam_sm_xxxx( )供PAM接口库调用，将不同的认证机制加入到系统中；PAM接口库（libpam）则读取配置文件，将应用程序和相应的PAM服务模块联系起来   


* PAM认证机制  
PAM相关文件  
模块文件目录：/lib64/security/*.so  
环境相关的设置：/etc/security/  
主配置文件:/etc/pam.conf，默认不存在  
为每种应用模块提供一个专用的配置文件：/etc/pam.d/APP_NAME  
注意：如/etc/pam.d存在，/etc/pam.conf将失效  


* *am认证原理  
PAM认证一般遵循这样的顺序：Service(服务)→PAM(配置文件)→pam_*.so  
PAM认证首先要确定那一项服务，然后加载相应的PAM的配置文件(位于/etc/pam.d下)，最后调用认证文件(位于/lib/security下)进行安全认证  


* PAM认证机制  
PAM认证过程：  
1.使用者执行/usr/bin/passwd 程序，并输入密码  
2.passwd开始调用PAM模块，PAM模块会搜寻passwd程序的PAM相关设置文件，这个设置文件一般是在/etc/pam.d/里边的与程序同名的文件，即PAM会搜寻/etc/pam.d/passwd此设置文件  
3.经由/etc/pam.d/passwd设定文件的数据，取用PAM所提供的相关模块来进行验证  
4.将验证结果回传给passwd这个程序，而passwd这个程序会根据PAM回传的结果决定下一个动作（重新输入密码或者通过验证）  

* PAM认证机制  
通用配置文件/etc/pam.conf格式  
application type control module-path arguments  
专用配置文件/etc/pam.d/* 格式  
type control module-path arguments  
说明：  
服务名（application）  
telnet、login、ftp等，服务名字“OTHER”代表所有没有在该文件中明确配置的其它服务  
模块类型（module-type）  
control PAM库该如何处理与该服务相关的PAM模块的成功或失败情况  
module-path 用来指明本模块对应的程序文件的路径名  
Arguments 用来传递给该模块的参数  
 

* PAM认证机制  
模块类型（module-type）  
Auth 账号的认证和授权  
Account 与账号管理相关的非认证类的功能，如：用来限制/允许用户对某个服务的访问时间，当前有效的系统资源(最多可以有多少个用户)，限制用户的位置(例如：root用户只能从控制台登录)  
Password 用户修改密码时密码复杂度检查机制等功能  
Session 用户获取到服务之前或使用服务完成之后需要进行一些附加的操作，如：记录打开/关闭数据的信息，监视目录等  
-type 表示因为缺失而不能加载的模块将不记录到系统日志,对于那些不总是安装在系统上的模块有用  

* PAM认证机制  
Control:  
PAM库如何处理与该服务相关的PAM模块成功或失败情况  
两种方式实现：  
简单和复杂  
简单方式实现：一个关健词实现  
required ：一票否决，表示本模块必须返回成功才能通过认证，但是如果该模块返回失败，失败结果也不会立即通知用户，而是要等到同一type中的所有模块全部执行完毕再将失败结果返回给应用程序，即为必要条件    

* PAM认证机制  
requisite ：一票否决，该模块必须返回成功才能通过认证，但是一旦该模块返回失败，将不再执行同一type内的任何模块，而是直接将控制权返回给应用程序。是一个必要条件
sufficient ：一票通过，表明本模块返回成功则通过身份认证的要求，不必再执行同一type内的其它模块，但如果本模块返回失败可忽略，即为充分条件  
optional ：表明本模块是可选的，它的成功与否不会对身份认证起关键作用，其返回值一般被忽略  
include： 调用其他的配置文件中定义的配置信息    

* PAM认证机制  
复杂详细实现：使用一个或多个“status=action”  
[status1=action1 status2=action …]  
Status:检查结果的返回状态  
Action:采取行为 ok，done，die，bad，ignore，reset  
ok 模块通过，继续检查  
done 模块通过，返回最后结果给应用  
bad 结果失败，继续检查  
die 结果失败，返回失败结果给应用  
ignore 结果忽略，不影响最后结果  
reset 忽略已经得到的结果   


* PAM认证机制  
module-path: 模块路径  
相对路径：  
/lib64/security目录下的模块可使用相对路径  
如：pam_shells.so、pam_limits.so  
绝对路径：  
模块通过读取配置文件完成用户对系统资源的使用控制  
/etc/security/*.conf  
注意：修改PAM配置文件将马上生效  
建议：编辑pam规则时，保持至少打开一个root会话，以防止root身份验证错误 
Arguments 用来传递给该模块的参数 

* pam文档说明  
/user/share/doc/pam-*  
rpm -qd pam  
man –k pam_  
man 模块名 如man rootok  
《The Linux-PAM System Administrators' Guide》  

* PAM模块示例   
模块：pam_shells  
功能：检查有效shell  
man pam_shells  
示例：不允许使用/bin/csh的用户本地登录  
vim /etc/pam.d/login  
auth required pam_shells.so  
vim /etc/shells  
去掉 /bin/csh  
useradd –s /bin/csh testuser  
testuser将不可登录  
tail /var/log/secure  

* PAM模块示例
模块：pam_securetty.so  
功能：只允许root用户在/etc/securetty列出的安全终端上登陆   
示例：允许root在telnet登陆  
vi /etc/pam.d/remote  
\#auth required pam_securetty.so #将这一行加上注释
或者/etc/securetty文件中加入  
pts/0,pts/1…pts/n 

* PAM模块示例  
模块：pam_nologin.so  
功能：  
如果/etc/nologin文件存在,将导致非root用户不能登陆  
如果用户shell是/sbin/nologin 时，当该用户登陆时，会显示/etc/nologin文件内容，并拒绝登陆  
 
* PAM模块示例  
模块：pam_limits.so
功能：在用户级别实现对其可使用的资源的限制，例如：可打开的文件数量，可运行的进程数量，可用内存空间
修改限制的实现方式：
(1) ulimit命令，立即生效，但无法保存
-n 每个进程最多的打开的文件描述符个数
-u 最大用户进程数
-S 使用 soft（软）资源限制
-H 使用 hard（硬）资源限制
(2) 配置文件：/etc/security/limits.conf, /etc/security/limits.d/*.conf
配置文件：每行一个定义；
<domain> <type> <item> <value>

* pam_limits.so  
<domain> 应用于哪些对象  
Username 单个用户  
@group 组内所有用户  
*所有用户  
<type> 限制的类型  
Soft 软限制,普通用户自己可以修改  
Hard 硬限制,由root用户设定，且通过kernel强制生效  
- 二者同时限定  
<item> 限制的资源  
nofile 所能够同时打开的最大文件数量,默认为1024  
nproc 所能够同时运行的进程的最大数量,默认为1024  
<value> 指定具体值   

* 示例：pam_limits.so  
限制用户最多打开的文件数和运行进程数  
/etc/pam.d/system-auth  
session required pam_limits.so  
vim /etc/security/limits.conf  
apache – nofile 10240 用户apache可打开 10240个文件  
student hard nproc 20 用户student不能运行超过20个进程  


wang - nproc     0 



```bash  
[root@Centos6 pam.d]#ulimit -a
core file size          (blocks, -c) 0
data seg size           (kbytes, -d) unlimited
scheduling priority             (-e) 0
file size               (blocks, -f) unlimited
pending signals                 (-i) 7234
max locked memory       (kbytes, -l) 64
max memory size         (kbytes, -m) unlimited
open files                      (-n) 1024
pipe size            (512 bytes, -p) 8
POSIX message queues     (bytes, -q) 819200
real-time priority              (-r) 0
stack size              (kbytes, -s) 10240
cpu time               (seconds, -t) unlimited
max user processes              (-u) 7234
virtual memory          (kbytes, -v) unlimited
file locks                      (-x) unlimited   
```   
测试性能
ab -c 100  -2000  http://192.168.228. 
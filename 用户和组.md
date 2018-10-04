
# 用户、组和权限  
## 本章内容  
1. 解释Linux的安全模型   
2. 解释用户帐号和组群帐号的目的  
3. 用户和组管理命令   
4. 理解并设置文件权限
5. 默认权限 
6. 特殊权限   
7. ACL  

*** 
1.1 介绍安全3A  
资源分派：   
Authentication：认证   
Authorization：授权   
Accouting|Audition：审计  

2.1 用户user
令牌token,identity   
Linux用户：Username/UID  
管理员：root, 0   
普通用户：1-60000 自动分配   
系统用户：1-499, 1-999 （CentOS7） 对守护进程获取资源进行权限分配 登录用户：500+, 1000+（CentOS7）   
交互式登录

1.2 组group  
Linux组：Groupname/GID  
管理员组：root, 0   
普通组： 系统组：1-499, 1-999（CENTOS7）   
普通组：500+, 1000+（CENTOS7）  

2.3 安全上下文  
Linux安全上下文   
运行中的程序：进程 (process) 以进程发起者的身份运行：   
root: /bin/cat  
mage: /bin/cat   
进程所能够访问资源的权限取决于进程的运行者的身份  

2.4 组的类别  
Linux组的类别   
用户的主要组(primary group) 用户必须属于一个且只有一个主组 组名同用户名，且仅包含一个用户，私有组 用户的附加组(supplementary group) 一个用户可以属于零个或多个辅助组

2.5 用户和组的配置文件  
Linux用户和组的主要配置文件：  
 * /etc/passwd：用户及其属性信息(名称、UID、主组ID等）  
 * /etc/group：组及其属性信息  
 * /etc/shadow：用户密码及其相关属性  
 * /etc/gshadow：组密码及其相关属性  

 2.6 passwd文件格式  
 * login name：登录用名（wang）   
 * passwd：密码 (x)   
 * UID：用户身份编号 (1000)  
 * GID：登录默认所在组编号 (1000)  
 * GECOS：用户全名或注释   
 * home directory：用户主目录 (/home/wang)   
 * shell：用户默认使用shell (/bin/bash)   
 ```bash
 [root@Centos7 ~]#cat /etc/passwd
 root:x:0:0:root:/root:/bin/bash 
 ```

2.6 shadow文件格式  
 * 登录用名   
 * 用户密码:一般用sha512加密  
 * 从1970年1月1日起到密码最近一次被更改的时间  
 * 密码再过几天可以被变更（0表示随时可被变更） 
 * 密码再过几天必须被变更（99999表示永不过期） 
 * 密码过期前几天系统提醒用户（默认为一周）   
 * 密码过期几天后帐号会被锁定 从1970年1月1日算起，多少天后帐号失效
```bash
[root@Centos7 ~]#cat /etc/shadow
mage:!!:17806:0:99999:7:::   
```  

2.7 密码加密   
加密机制： 加密：明文--> 密文 解密：密文--> 明文  
 单向加密：哈希算法，原文不同，密文必不同 相同算法  
 &ensp; &ensp; &ensp; &ensp; &ensp; &ensp; &ensp;定长输出，获得密文不可逆推出原始数据   
 &ensp; &ensp; &ensp; &ensp; &ensp; &ensp; &ensp;雪崩效应：初始条件的微小改变，引起结果的巨大改  
 &ensp; &ensp; &ensp; &ensp; &ensp; &ensp; &ensp;md5: message digest, 128bits  
 &ensp; &ensp; &ensp; &ensp; &ensp; &ensp; &ensp;sha1: secure hash algorithm, 160bits   
 &ensp; &ensp; &ensp; &ensp; &ensp; &ensp; &ensp;sha224: 224bits sha256: 256bits   
 &ensp; &ensp; &ensp; &ensp; &ensp; &ensp; &ensp;sha384: 384bits sha512: 512bits   
 &ensp; &ensp; &ensp; &ensp; &ensp; &ensp; &ensp;更改加密算法：  
 &ensp; &ensp; &ensp; &ensp; &ensp; &ensp; &ensp;authconfig  --passalgo=sha256 --update  

 2.8密码的复杂性策略  
  * 使用数字、大写字母、小写字母及特殊字符中至少3种  
  * 足够长   
  * 使用随机密码   
  * 定期更换,不要使用最近曾经使用过的密码


1.9 group文件格式  
* 群组名称：就是群组名称  
* 群组密码：通常不需要设定，密码是被记录在 
*  /etc/gshadow   
* GID：就是群组的 ID   以当前组为附加组的用户列表(分隔符为逗号)  
```bash
[root@Centos7 ~]#cat /etc/group
root:x:0:gentoo  
```

1.10 gshdow文件格式  
* 群组名称：就是群组名称   
* 群组密码：  
* 组管理员列表：组管理员的列表，更改组密码和成员  
* 以当前组为附加组的用户列表：(分隔符为逗号)  
```bash  
[root@Centos7 ~]#cat /etc/gshadow
root:::gentoo  
```

1.11 文件操作  
* vipw和vigr   
* pwck和grpck  

2.1 用户和组管理命令
* useradd   
* usermod   
* userdel  
组帐号维护命令   
* groupadd  
* groupmod   
* groupdel  

2.2 用户创建：useradd  
* useradd [options] LOGIN  
 -u UID -o 配合-u 选项，不检查UID的唯一性   -g GID：指明用户所属基本组，可为组名，也可以GID   
 -c "COMMENT"：用户的注释信息   
 -d HOME_DIR: 以指定的路径(不存在)为家目录  
 -s SHELL: 指明用户的默认shell程序，可用列表在/etc/shells文件中   
 -G GROUP1[,GROUP2,...]：为用户指明附加组，组须事先存在   
 -N 不创建私用组做主组，使用users组做主组   
 -r: 创建系统用户 CentOS 6: ID<500，CentOS 7: ID<1000   
 -m  创建家目录，用于系统用户   
 -M  不创建家目录，用于非系统用户    
 ```bash   
 [root@Centos7 ~]#useradd mage
 ```

 创建用户： useradd   
 * 默认值设定：/etc/default/useradd文件中   
 显示或更改默认设置  
 * useradd -D   
 * useradd –D -s SHELL   
 * useradd –D –b BASE_DIR  

2.3 新建用户的相关文件和命令  
* /etc/default/useradd   
* /etc/skel/*  
* /etc/login.defs   
* newusers  passwd格式文件 批量创建用户 
*  chpasswd  批量修改用户口令  

2.4 用户属性修改  
* usermod [OPTION] login   
-u UID: 新UID   
-g GID: 新主组   
-G GROUP1[,GROUP2,...[,GROUPN]]]：新附加组，原来的附加组将会被 覆盖；若保留原有，则要同时使用  -a选项   
-s SHELL：新的默认SHELL   
-c 'COMMENT'：新的注释信息   
-d HOME: 新家目录不会自动创建；若要创建新家目录并移动原家数据， 同时使用-m选项   
-l login_name: 新的名字；   
-L: lock指定用户,在/etc/shadow 密码栏的增加 !   
-U: unlock指定用户,将 /etc/shadow 密码栏的 ! 拿掉   
-e YYYY-MM-DD: 指明用户账号过期日期   
-f INACTIVE: 设定非活动期限
 
2.5 删除用户  
* userdel [OPTION]... login   
-r: 删除用户家目录  

2.6 查看用户相关的ID信息  
* id [OPTION]... [USER]  
-u: 显示UID -g: 显示GID   
-G: 显示用户所属的组的ID   
-n: 显示名称，需配合ugG使用

2.6 切换用户或以其他用户身份执行命令  
* su [options...] [-] [user [args...]]  
 切换用户的方式：   
 su UserName：非登录式切换，即不会读取目标用户的配置文件，不改变 当前工作目录    
 su - UserName：登录式切换，会读取目标用户的配置文件，切换至家目 录，完全切换      
 root su至其他用户无须密码；非root用户切换时需要密码     
 换个身份执行命令： su [-] UserName -c 'COMMAND'     
 选项：-l  --login  
  su -l UserName 相当于 su - UserName  

2.7 设置密码  
* passwd [OPTIONS] UserName: 修改指定用户的密码   
常用选项：   
-d：删除指定用户密码   
-l：锁定指定用户  
-u：解锁指定用户  
-e：强制用户下次登录修改密码  
-f：强制操作   
-n mindays：指定最短使用期限   
-x maxdays：最大使用期限   
-w warndays：提前多少天开始警告 -i inactivedays：非活动期限   
--stdin：从标准输入接收用户密码 echo "PASSWORD" | passwd --stdin USERNAME  

2.8 修改用户密码策略
* chage [OPTION]... LOGIN   
-d LAST_DAY   
-E --expiredate EXPIRE_DATE   
-I --inactive INACTIVE -m --mindays MIN_DAYS   
-M --maxdays MAX_DAYS   
-W --warndays WARN_DAYS  
–l  显示密码策略  
 示例： chage -d 0 tom 下一次登录强制重设密码 chage -m 0 –M 42 –W 14 –I 7 tom   
 chage -E 2016-09-10 tom  

2.9 用户相关的其它命令  
* chfn 指定个人信息  
* chsh 指定shell  
* finger  

2.10 创建组  
 * groupadd[OPTION]... group_name
-g GID: 指明GID号；[GID_MIN, GID_MAX]   
-r: 创建系统组  
 CentOS 6: ID<500   
 CentOS 7: ID<1000

2.11 修改和删除组  
* 组属性修改:groupmod   
groupmod [OPTION]... group   
-n group_name: 新名字  
-g GID: 新的GID

组删除：groupdel   
* groupdel GROUP  

2.12 更改组密码  
* 组密码：gpasswd   
gpasswd [OPTION] GROUP   
-a user  将user添加至指定组中    
-d user  从指定组中移除用户user -A user1,user2,...  设置有管理权限的用户列表   
newgrp命令：临时切换主组  ,如果用户本不属于此组，则需要组密码  

2.13 更改和查看组成员
* groupmems [options] [action]  
options：   
-g, --group groupname 更改为指定组 (只有root) Actions:   
-a, --add username     指定用户加入组   
-d, --delete username 从组中删除用户   
-p, --purge 从组中清除所有成员   
-l,  --list 显示组成员列表   
groups [OPTION].[USERNAME]... 查看用户所属组列表

![user and group](https://raw.githubusercontent.com/SugarSir/linux-/df617f31cb2309c57ec244c9b28c9099096d67ad/pictrue/b09bf10d5fc0db803db496c4b6856205_20161030002905428800_1.jpg)




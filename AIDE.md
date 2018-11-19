#### AIDE
当一个入侵者进入了你的系统并且种植了木马，通常会想办法来隐蔽这个木马（除了木马自身的一些隐蔽特性外，他会尽量给你检查系统的过程设置障碍），通常入侵者会修改一些文件，比如管理员通常用ps -aux来查看系统进程，那么入侵者很可能用自己经过修改的ps程序来替换掉你系统上的ps程序，以使用ps命令查不到正在运行的木马程序。如果入侵者发现管理员正在运行crontab作业，也有可能替换掉crontab程序等等。所以由此可以看出对于系统文件或是关键文件的检查是很必要的。目前就系统完整性检查的工具用的比较多的有两款：Tripwire和AIDE，前者是一款商业软件，后者是一款免费的但功能也很强大的工具  

* AIDE
安装
yum install aide  
修改配置文件  
vim /etc/aide.conf (指定对哪些文件进行检测)  
/test/chameleon R /bin/ps R+a /usr/bin/crontab R+a  
/etc PERMS  
!/etc/mtab #“!”表示忽略这个文件的检查  
R=p+i+n+u+g+s+m+c+md5 权限+索引节点+链接数+用户+组+大小+最后一 次修改时间+创建时间+md5校验值  
NORMAL = R+rmd60+sha256  


* AIDE  
初始化默认的AIDE的库：  
/usr/local/bin/aide --init  
生成检查数据库（建议初始数据库存放到安全的地方）  
cd /var/lib/aide  
mv aide.db.new.gz aide.db.gz  
检测： /usr/local/bin/aide --check  
更新数据库  
[root@Centos7 aide]#aide --update 

安装aide 软件
```bash  
[root@Centos7 aide]#yum install aide   
```  
查看aide信息
```bash   
[root@Centos7 aide]#rpm -ql aide  

[root@Centos7 dropbear-2018.76]#rpm -qi aide   
```  
修改aide 配置文件    
```bash  
[root@Centos7 aide]#vim /etc/aide.conf   
```  
初始化aide数据库  
```bash   
[root@Centos7 aide]#aide --init  
```   
更新aide 数据库  
```bash   
[root@Centos7 aide]#aide --update   
```  
更改aide.db.new.gz 名字  
```bash  
[root@Centos7 aide]#mv aide.db.new.gz aide.db.gz    
```  
对比  
```bash  
[root@Centos7 aide]#aide -C   

AIDE, version 0.15.1

### All files match AIDE database. Looks okay!

```  

* 补充：   
/etc/aide.conf    
/root/\..* PERMS  
/etc/rsyslog.conf$   
该文件支持正则表达式
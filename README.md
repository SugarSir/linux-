
# 文件查找和压缩  
## 文件查找  
* 文件查找：locate、 find   
locate :非实时查找（基于数据库查找）  
find : 实时查找  

## locate
* 查询系统上预建的文件索引:/var/lib/mlocate/mlocate.db  
* 依赖于事先构建的索引  
索引的构建是在系统较为空闲时自动进行（周期进行），管理员手动更新数据库（updatedb）  
索引构建过程需要构建根文件系统，及消耗资源  
* 工作特点：
•查找速度快  
•模糊查找  
•非实时查找  
•搜索的是文件的全路径，不仅仅是文件名  
•可能只搜索用户具备读取和执行权限的目录   

### locate命令  
* locate KEYWORD  
有用的选项  
-i不区分大小写的搜索  
-n N只列举前N个匹配项目  
-r 使用正则表达式    
```bash  
[root@centos7 date]#locate -r '.*zip'  
```

## find  
* 语法  
* find [OPTION]... [查找路径] [查找条件] [处理动作]     
查找路径：指定具体目标路径；默认为当前目录  
查找条件：指定的查找标准，可以文件名、大小、类型、权限等标准进行；默认为找出指定路径下的所有文件  
处理动作：对符合条件的文件做操作，默认输出至屏幕   

### 查找条件
* 指搜索层级
-maxdepthlevel 最大搜索目录深度,指定目录为第1级  
-mindepthlevel 最小搜索目录深度 

* 先处理目录内的文件，再处理目录    
-depth

* 根据文件名和inode查找：  
-name "文件名称"：支持使用glob
*, ?, [], [^]    
```bash  
[root@centos7 date]#find /date -name *.zip  
```
-iname"文件名称"：不区分字母大小写  
-inumn 按inode号查找  
-samefilename 相同inode号的文件  
-links n 链接数为n的文件  
-regex“PATTERN”：以PATTERN匹配整个文件路径，而非文件名称  

* 根据属主、属组查找：  
-user USERNAME：查找属主为指定用户(UID)的文件  
-group GRPNAME: 查找属组为指定组(GID)的文件  
-uidUserID：查找属主为指定的UID号的文件  
-gidGroupID：查找属组为指定的GID号的文件  
-nouser：查找没有属主的文件  
-nogroup：查找没有属组的文件    
```bash  
[root@centos7 date]#find /date -user tang  
```

* 根据文件类型查找：  
-type TYPE:  
f: 普通文件  
d: 目录文件  
l: 符号链接文件  
s：套接字文件  
b: 块设备文件  
c: 字符设备文件  
p: 管道文件    

```bash  
[root@centos7 date]#find /dev/ -type s        
/dev/log   
```  
* 空文件或目录  
-empty  
find /app -type d -empty  


*  组合条件：  
与：-a  
或：-o   
非：-not,!  
德·摩根定律：  
(非A) 或(非B) = 非(A 且B)  
(非A) 且(非B) = 非(A 或B)  
示例：  
!A -a !B = !(A -o B)  
!A -o !B = !(A -a B)    

找出/tmp目录下，属主不是root，且文件名不以f开头的文件  
```bash    
find /tmp \( -not -user root -a -not -name 'f*' \) -ls  
find /tmp -not \( -user root -o -name 'f*' \) –ls      
```

```bash  
[root@centos7 date]#find /etc/ -user root  -a -type l  
```

#### find示例 : 
```bash    
find -name snow.png  
find -iname snow.png  
find  / -name  “*.txt” 
find /var –name “*log*”  
find -user joe -group joe  
find -user joe -not-group joe  
find -user joe -o -user jane  
find -not \(-userjoe-o-userjane\)  ()需要转义
find  / -userjoe-o-uid500  
```  

* 排除目录  
示例：  
查找/etc/下，除/etc/sane.d目录的其它所有.conf后缀的文件
```bash  
find /etc -path "/etc/sane.d" -a -prune -o -name "*.conf"  
```  
查找/etc/下，除/etc/sane.d和/etc/fonts两个目录的所有.conf后缀的文件 
```bash 
find /etc \( -path "/etc/sane.d" -o -path "/etc/fonts" \) -a -prune -o -name "*.conf"
```  

* 根据文件大小来查找：  
-size [+|-]#UNIT  
常用单位：k, M, G，c（byte）   
#UNIT: (#-1, #] 
如：6k 表示(5k,6k]
-#UNIT：[0,#-1]  
如：-6k 表示[0,5k] 
+#UNIT：(#,∞)  
如：+6k 表示(6k,∞) 

```bash  
[root@centos7 date]#find /date/ +10M   
```  
* 根据时间戳：    
以“天”为单位  
-atime[+|-]#,  
#: [#,#+1)  
+#: [#+1,∞]  
-#: [0,#)  
-mtime  
-ctime  
以“分钟”为单位  
-amin  
-mmin  
-cmin    


* 根据权限查找：  
-perm [/|-]MODE  
MODE: 精确权限匹配  
/MODE：任何一类(u,g,o)对象的权限中只要能一位匹配即可，或关系，+ 从centos7开始淘汰  
-MODE：每一类对象都必须同时拥有指定权限，与关系  
0 表示不关注   
•find -perm755会匹配权限模式恰好是755的文件  
•只要当任意人有写权限时，find -perm +222就会匹配  
•只有当每个人都有写权限时，find -perm -222才会匹配  
•只有当其它人（other）有写权限时，find -perm -002才会匹配    
```bash   
[root@centos7 date]#find -perm 644 
```  

### 处理动作  

-print：默认的处理动作，显示至屏幕  
-ls：类似于对查找到的文件执行“ls -l”命令  
-delete：删除查找到的文件  
-fls file：查找到的所有文件的长格式信息保存至指定文件中  
-ok COMMAND {} \; 对查找到的每个文件执行由COMMAND指定的命令，对于每个文  件执行命令之前，都会交互式要求用户确认    
-exec COMMAND {} \; 对查找到的每个文件执行由COMMAND指定的命令  (非交互式不提示)
{}: 用于引用查找到的文件名称自身  
find传递查找到的文件至后面指定的命令时，查找到所有符合条件的文件一次性 传递给后面的命令
处理动作
```bash   
[root@centos7 date]#find -perm 644 -a -name *33:1 -delete  
```

```bash  
[root@centos7 date]#find -perm 644 -name "file*" -ok mv {} /root/mile/ \;  
```  

### 参数替换xargs  
由于很多命令不支持管道|来传递参数，而日常工作中有这个必要，所以就有了xargs命令  
* xargs用于产生某个命令的参数，xargs可以读入stdin的数据，并且以空格符或回车符将stdin的数据分隔成为arguments  
注意：文件名或者是其他意义的名词内含有空格符的情况  
有些命令不能接受过多参数，命令执行可能会失败，xargs可以解决  
示例：  
ls f* |xargsrm  
find /sbin -perm +700 |ls -l 这个命令是错误的  
find /sbin -perm +7000 | xargs ls –l 查找特殊权限的文件  
find和xargs格式：find | xargs COMMAND   

```bash  
[root@centos7 date]#echo file{1..65535} |xargs touch   
```  
这种形式可以创建大量的文件  

#### find示例：  
* 备份配置文件，添加.orig这个扩展名
```bash 
find -name “*.conf” -exec cp {} {}.orig\;
```
* 提示删除存在时间超过３天以上的joe的临时文件 
```bash 
find /tmp -ctime +3 -user joe -ok rm {} \;
```
* 在主目录中寻找可被其它用户写入的文件  
```bash  
find ~ -perm -002 -execchmodo-w{}\;
```
* 查找/data下的权限为644，后缀为sh的普通文件，增加执行权限  
```bash
find /data –type f -perm 644 -name '*.sh' –exec chmod755 {} \;
```
* 查看/home的目录
```bash
find /home –type d -ls
```



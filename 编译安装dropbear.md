ssh协议的另一个实现：dropbear
源码编译安装：
1、安装开发包组:yum groupinstall “Development tools”
2、下载dropbear-2017.75.tar.bz2
3、tar xf dropbear-2017.75.tar.bz2
4、less INSTALL README
5、./configure
6、make PROGRAMS="dropbear dbclient dropbearkey dropbearconvert scp"
7、make PROGRAMS="dropbear dbclient dropbearkey dropbearconvert scp" install  
* 启动ssh服务：
8、ls /usr/local/sbin/ /usr/local/bin/
9、/usr/local/sbin/dropbear -h
10、mkdir /etc/dropbear
11、dropbearkey -t rsa -f /etc/dropbear/dropbear_rsa_host_key -s 2048
12、dropbearkey -t dss -f /etc/dropbear/dropbear_dsa_host_key
13、dropbear -p :2222 -F –E #前台运行
dropbear -p :2222 #后台运行
* 客户端访问：
14、ssh -p 2222 root@127.0.0.1
15、dbclient -p 2222 root@127.0.0.1

编译安装dropbear   

下载dropbear 软件包  
```bash   
 [root@Centos7 date]#wget https://matt.ucc.asn.au/dropbear/dropbear-2018.76.tar.bz2  
```  
解压软件包  
```bash  
[root@Centos7 date]#tar -xvf dropbear-2018.76.tar.bz2  
``` 

 [root@Centos7 date]#cd dropbear-2018.76/  
了解编译安装说明
```bash
[root@Centos7 dropbear-2018.76]#cat README 

[root@Centos7 dropbear-2018.76]#cat INSTALL  

[root@Centos7 dropbear-2018.76]#./configure --help  
```  
安装开发包组 
```bash  
[root@Centos7 dropbear-2018.76]#yum groupinstall "Development tools"  
```
```bash
[root@Centos7 dropbear-2018.76]#yum install zlib-devel
```  
指定安装和配置文件路径
```bash  
[root@Centos7 dropbear-2018.76]#./configure  --prefix=/app/dropbear/  --sysconfdir=/etc/dropbaer  
```   
创建配置文件目录
```bash  
[root@Centos7 dropbear-2018.76]#mkdir /etc/dropbear
```
编译安装  
```
[root@Centos7 dropbear-2018.76]#make PROGRAMS="dropbear dbclient dropbearkey dropbearconvert scp"

[root@Centos7 dropbear-2018.76]#make PROGRAMS="dropbear dbclient dropbearkey dropbearconvert scp" install
``` 
生成密钥
```bash
[root@Centos7 dropbear-2018.76]#./dropbearkey --help

[root@Centos7 bin]#./dropbearkey -t rsa -f /etc/dropbear/dropbear_dss_host_key
```  
开启dropbear ssh服务
```bash  
[root@Centos7 sbin]#./dropbear -FE -p 22222  
```  
通过ssh连接  
```bash  
[root@Centos7 ~]#ssh 192.168.228.183 -p 22222   
```   
创建/app/dropbear/bin/dbclient  软连接  
```bash  
[root@Centos7 bin]#ln -s /app/dropbear/bin/dbclient /usr/bin/dbclient  
```
```bash
[root@Centos6 ~]#scp /etc/fstab /date/  

```   
配置dropbear 的PATH变量
```bash  
[root@Centos7 bin]#echo '$PATH:/app/dropbear/bin/:/app/dropbear/sbin/' >/etc/profile.d/dropbear.sh 
```  

* 编译安装测试完成删除相关安装包 ，及配置文件 
```bash
[root@Centos7 /]#rm -rf app  

[root@Centos7 /]#rm -rf /etc/dropbear/  

[root@Centos7 /]#rm -f /etc/profile.d/dropbear.sh 

[root@Centos7 /]#rm -fr /date/dropbear/  

```   

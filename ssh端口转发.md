ssh  
ssh基于key 验证  
生成key对儿 
```bash  
[root@Centos7 ~]#ssh-keygen  
```  

```bash  
[root@Centos7 .ssh]#ssh-copy-id -i /root/.ssh/id_rsa 192.168.228.173  
```   
三台主机互联  
1. 在拥有私钥对的主机上执行同一个命令
```bash
[root@Centos7 .ssh]# ssh-copy-id 192.168.228.177  
```  
最终在192.168.228.177 的主机拥有其他主机的公钥  authorized_keys
然后将authorized_keys复制在其他主机上
最终实现三台主机互相远程
2. 三台主机共用一个私钥对



scp 和rsync 的区别
scp 即使文件存在他也会复制
rsync 有更新的作用，如果文件更改，他会同步更新，而不是重新复制个复制  
实现增量复制

pssh 

[root@Centos6 date]#pssh -h dir/iplist -o /date/test.txt -i getent passwd haha  

[root@Centos6 date]#pssh -h dir/iplist -i 'echo hostname'  
SSH端口转发  
本地转发：  
-L    localport:remotehost:remotehostport sshserver
选项：  
-f 后台启用  
-N 不打开远程shell，处于等待状态  
-g 启用网关功能  
示例  
ssh –L 9527:telnetsrv:23 -Nfg sshsrv
telnet 127.0.0.1 9527  
当访问本机的9527的端口时，被加密后转发到sshsrv的ssh服务，再解密被转发到telnetsrv:23  


实验 SSH端口转发  
1. 本地转发：
在contos6 上开启telnet-server 服务  
[root@Centos6 ~]#chkconfig telnet on  
[root@Centos6 ~]#service xinetd start  

telnet 192.168.28.181
通过防火墙拒绝192.168.228.183
iptable -A INPUT -s 192.168.228.183  -j REJECT  

通过跳板连接
ssh –L 9527:192.168.228.182:23 -N 192.168.228.178    

telnet 172.0.0.1 9527  

SSH端口转发  
 
-R   远程转发: sshserverport:remotehost:remotehostport sshserver  
示例：  
ssh –R 9527:telnetsrv:23 –Nf sshsrv  
让sshsrv侦听9527端口的访问，如有访问，就加密后通过ssh服务转发请求到本机ssh客户端，再由本机解密后转发到telnetsrv:23 
Data   sshsrv:9527   sshsrv:22   localhost:XXXXX   localhost:YYYYY   telnetsrv:23

2. 实验 远程转发:   

ssh -R 9527:192.168.228.182:23 -fN 192.168.228.183   

ss -nt 查看三台主机连接状况  
在192.168.228.183 主机上
telnet 127.0.0.1 9527 连接


 

3. 通过隧道作为堡垒机实现两边互通    
ssh -R 9527:192.168.228.182:23 -fNg 192.168.228.183   
修改配置文件  /etc/ssh/sshd.config  将Geteway  ports yes    


SSH端口转发  
动态端口转发：  
当用firefox访问internet时，本机的1080端口做为代理服务器，  firefox的访问请求被转发到 sshserver上，由sshserver替之访问internet  
ssh -D 1080 root@sshserver -fNg
在本机firefox设置代理socket  proxy:127.0.0.1:1080  
curl --socks5 127.0.0.1:1080  http://www.qq.com 

 
  

4. 动态端口转发  
ssh -D 1080 192.168.228.178  -fNG  
在本机firefox设置代理socket  
procy:192.168.228.183:1080  
curl --socks5 192.168.228.183:1080   192.168.228.182   






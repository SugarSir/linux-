* ssh服务器  
服务器端：sshd, 配置文件: /etc/ssh/sshd_config  
常用参数：  
Port    绑定登陆端口
ListenAddress ip   绑定监听地址
LoginGraceTime 2m  登陆等待时长
PermitRootLogin yes  
StrictModes yes 检查.ssh/文件的所有者，权限等  
MaxAuthTries 6   
MaxSessions 10 同一个连接最大会话  
PubkeyAuthentication yes  
PermitEmptyPasswords no  禁止空口令登陆
PasswordAuthentication yes  
常用参数
GatewayPorts no
ClientAliveInterval 单位:秒  内多少秒检查一次
ClientAliveCountMax 默认3 检查次数
UseDNS yes
GSSAPIAuthentication yes 提高速度可改为no
MaxStartups 未认证连接最大值，默认值10 （最大并发连接量）  
Banner /path/file   登陆提示
限制可登录用户的办法：
AllowUsers user1 user2 user3
DenyUsers
AllowGroups
DenyGroups    

* ssh服务的最佳实践   
建议使用非默认端口   
禁止使用protocol version 1  
限制可登录用户  
设定空闲会话超时时长  
利用防火墙设置ssh访问策略  
仅监听特定的IP地址  
基于口令认证时，使用强密码策略  
tr -dc A-Za-z0-9_ < /dev/urandom | head -c 12| xargs  
使用基于密钥的认证  
禁止使用空密码  
禁止root用户直接登录  
限制ssh的访问频度和并发在线数  
经常分析日志
```bash    
[root@Centos7 ~]#openssl rand -base64 12
B+dY9fJIb77UQFIG  
```

补充：
取出登录失败的ip 并且达到多少次可以扔进防火墙，拒绝登陆
```bash
[root@Centos7 ~]#awk '/Failed password/{ip[$(NF-3)]++}END{for(i in ip){print i ip[i]}}' /var/log/secure |sor -K2 -nr  
```
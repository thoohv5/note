# Ssh

## 什么是ssh

简单来说，ssh是一种网络协议，用于计算机之间的加密登录。

## 中间人攻击

SSH之所以能够保证安全，原因在于它采用了公钥加密。整个过程如下：

（1）远程主机收到用户的登录请求，把自己的公钥发给用户。

（2）用户使用这个公钥，将登录密码加密后，发送回来。

（3）远程主机用自己的私钥，解密登录密码，如果密码正确，就同意用户登录。

这个过程本身是安全的，但是实施的时候存在一个风险：如果有人截获了登录请求，然后冒充远程主机，将伪造的公钥发给用户，那么用户很难辨别真伪。因为不像https协议，SSH协议的公钥是没有证书中心（CA）公证的，也就是说，都是自己签发的。可以设想，如果攻击者插在用户与远程主机之间（比如在公共的wifi区域），用伪造的公钥，获取用户的登录密码。再用这个密码登录远程主机，那么SSH的安全机制就荡然无存了。这种风险就是著名的"中间人攻击"。

## 配置文件

```yaml
# 默认端口号， 可以使用多个端口号
#Port 22
#Port 443

# 协议家族, IPV4 or IPV6
#AddressFamily any
# 监听地址
#ListenAddress 0.0.0.0 
# 指明IPV6的所有地址格式
#ListenAddress ::

# 当前版本支持的密钥认证方式
# rsa私钥认证
HostKey /etc/ssh/ssh_host_rsa_key
# ecdsa私钥认证
HostKey /etc/ssh/ssh_host_ecdsa_key
# ed25519私钥认证
HostKey /etc/ssh/ssh_host_ed25519_key

# Logging #关于日志文件的信息数据放置与daemon的名称
# 有人使用SSH登录系统的时候，SSH会记录信息，记录在/var/log/secure
#SyslogFacility AUTH
# 默认是以AUTH来设置的
SyslogFacility AUTHPRIV
# 日志等级
#LogLevel INFO

# 认证
# Authentication:
# 出现输入密码画面，多长时间没有成功连接上SSH Server就断线。
#LoginGraceTime 2m
# 是否允许管理员远程登录
PermitRootLogin yes
# sshd去检查用户主目录或相关文件的权限数据， 当用户的host key改变之后，Server不接受联机
#StrictModes yes

# 最大尝试次数               
#MaxAuthTries 6
# 允许最大会话数
#MaxSessions 10

# 是否允许Public Key
#PubkeyAuthentication yes

# 选择基于密钥验证时，客户端生成一对公私钥后，公钥放到.ssh/authorized_keys, 
# The default is to check both .ssh/authorized_keys and .ssh/authorized_keys2
# but this is overridden so installations will only check .ssh/authorized_keys
AuthorizedKeysFile      .ssh/authorized_keys

# 发送欢迎词的文件，none表示不发送             
#AuthorizedPrincipalsFile none

# 指定脚本查找用户的公钥文件做认证，在登录认证层面调用，可接受的参数有用户尝试登录时使用的私钥
#AuthorizedKeysCommand none

# 指定查找的用户
#AuthorizedKeysCommandUser nobody

# 是否信任~/.ssh/known_hosts文件
# For this to work you will also need host keys in /etc/ssh/ssh_known_hosts
#HostbasedAuthentication no
# Change to yes if you don't trust ~/.ssh/known_hosts for
# HostbasedAuthentication
# 忽略用户known_hosts文件
#IgnoreUserKnownHosts no
# 是否取消使用~/.ssh/.rhosts来作为认证。
# Don't read the user's ~/.rhosts and ~/.shosts files
#IgnoreRhosts yes

# 是否需要密码验证
# To disable tunneled clear text passwords, change to no here!
#PasswordAuthentication yes
# 是否允许空密码
#PermitEmptyPasswords no
PasswordAuthentication yes 

# 是否允许质疑-应答(challenge-response)认证。默认值是"yes"，所有 login.conf中允许的认证方式都被支持。
# Change to no to disable s/key passwords
#ChallengeResponseAuthentication yes
ChallengeResponseAuthentication no

# Kerberos认证服务器的选项
# Kerberos options
#KerberosAuthentication no
#KerberosOrLocalPasswd yes
#KerberosTicketCleanup yes
#KerberosGetAFSToken no
#KerberosUseKuserok yes

# 通用安全服务应用程序接口选项
# GSSAPI options
GSSAPIAuthentication yes
GSSAPICleanupCredentials no
#GSSAPIStrictAcceptorCheck yes
#GSSAPIKeyExchange no
#GSSAPIEnablek5users no
# PAM: Pluggable Authentication Modules 可插拔认证模块
# 使用PAM模块认证
UsePAM yes 

# 允许经过代理转发
#AllowAgentForwarding yes
# 允许经过TCP转发
#AllowTcpForwarding yes
# 转发路径端口
#GatewayPorts no
# X-window的设置
X11Forwarding yes
# x-window的设置
#X11DisplayOffset 10
# x-window的设置
#X11UseLocalhost yes
# 允许tty
#PermitTTY yes
# 登录后是否要显示信息，即使读取/etc/motd文件内容
# It is recommended to use pam_motd in /etc/pam.d/sshd instead of PrintMotd,
# as it is more configurable and versatile than the built-in version.
PrintMotd no

# 显示上次登录信息
#PrintLastLog yes
# SSH Server会传送KeepAlive信息给Client，确保联机正常  
#TCPKeepAlive yes        
# 若有一方脱机后，SSh可以立即知道      
#PermitUserEnvironment no
# 压缩延迟
#Compression delayed
# 设置空闲登录的最大时长
#ClientAliveInterval 0
# 在没收到任何数据的时候，最多向3次客户端进行keepalive检测
#ClientAliveCountMax 3
# 显示补丁级别
#ShowPatchLevel no
# 开启DNS解析
#UseDNS no
# 存储ssh的pid文件 
#PidFile /var/run/sshd.pid
# 最大可以保持多少个未认证的连接
#MaxStartups 10:30:100
# 允许tun设备转发
#PermitTunnel no
# 是否允许切换目录
#ChrootDirectory none
# 配置附加版本
#VersionAddendum none

# 不设置欢迎词
# no default banner path
#Banner none

# Accept locale-related environment variables 接受本地相关环境变量类型

# 是否开启sftp服务
# override default of no subsystems
Subsystem       sftp    /usr/libexec/openssh/sftp-server

# Example of overriding settings on a per-user basis
#Match User anoncvs
#       X11Forwarding no #是否开启X11转发
#       AllowTcpForwarding no #是否允许TCP转发
#       PermitTTY no #是否允许tty
#       ForceCommand cvs server #限制命令
```
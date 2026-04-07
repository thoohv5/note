# GitLab

<aside>
💡

使用root权限登录到CentOS系统

</aside>

# 依赖

```bash
# 依赖项
sudo yum install -y curl openssh-server postfix

# 启动并设置 sshd和 postfix服务为开机自启
sudo systemctl enable sshd postfix
sudo systemctl start sshd postfix
```

# 安装

```bash
# 添加GitLab的官方仓库：
curl -sS https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.rpm.sh | sudo bash

# 安装GitLab
sudo yum install -y gitlab-ce

# 离线安装
https://mirrors.tuna.tsinghua.edu.cn/gitlab-ce/yum/el7/

yum install -y perl

rpm -ivh gitlab-ce-17.1.1-ce.0.el7.x86_64.rpm

```

# 配置

```bash
sudo vi /etc/gitlab/gitlab.rb

external_url 'http://ip:port'
nginx['listen_https'] = false
nginx['listen_port'] = port
nignx['listen_address'] = ['*']

vim /var/opt/gitlab/nginx/conf/gitlab-http.conf
#修改如下
server {
	listen *:port;
	server_name ip
	if ($http_host = ""){
		set $http_host_with_default "ip:port";
	}
}

# 加载配置
sudo gitlab-ctl reconfigure
```

# 防火墙

```bash
sudo firewall-cmd --permanent --zone=public --add-service=http
sudo firewall-cmd --permanent --zone=public --add-service=https
sudo firewall-cmd --permanent --zone=public --add-service=ssh

sudo firewall-cmd --reload

firewall-cmd --zone=public --add-port=8001/tcp --permanent
```

# 命令

```bash
## 开机自起
systemctl start gitlab-runsvdir.service
 
## 关闭
systemctl disable gitlab-runsvdir.service

## 启动
gitlab-ctl start
 
## 重启
gitlab-ctl restart
 
## 停止
gitlab-ctl stop
 
## 重新加载配置文件
gitlab-ctl reconfigure
 
## 查看状态
gitlab-ctl status
 
## 检查gitlab
gitlab-rake gitlab:check SANITIZE=true
 
## 查看日志
gitlab-ctl tail 
 
## gitlab是根据hash值来保存的路径，这个值是项目id,项目id在每个项目的设置页面可以找到
echo -n  22 | sha256sum
```

# 密码

```bash
# 密码
/etc/gitlab/initial_root_password

```

## 登录控制台修改初始密码

```makefile

# 进入控制台
gitlab-rails console -e production

# 查询id为1的用户，id为1的用户是超级管理员
user = User.where(id:1).first

# 修改密码为lqz123456
user.password='#Ab123456'

# 保存
user.save!

# 退出
exit
```
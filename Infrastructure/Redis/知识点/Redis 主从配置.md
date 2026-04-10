# Redis 主从配置

## 配置

### 官网

```bash
[https://redis.io/download](https://redis.io/download)
```

### 环境

```bash
yum -y install gcc
yum -y install gcc-c++
#检查是否安装GCC
yum list installed | grep "gcc"   #只显示一个为未安装
```

### 编译

```bash
tar -zxvf redis-xxx.tar.gz
cd redis-xxx

make && make install

mv redis-xxx /usr/local/redis

cp 
```

### 修改配置

```bash
# 守护线程的方式启动
daemonize yes

# 监听地址
bind 0.0.0.0

# 节点密码
requirepass <password>

# 远程连接
protected-mode yes
```

<aside>
💡

</aside>

### 修改主从配置

```bash
slaveof <masterip> <mastetport>
masterauth <password>
```

## 主从手动切换

```bash
# 从 => 主
slaveof no one

# 主 => 从
slaveof <masterip> <masterport> 
config set masterauth <password>
```

```bash
redis重启
第一步：存储复制信息

redis在关闭时，通过shutdown save,都会调用rdbSaveInfoAuxFields函数，把当前实例的repl-id和repl-offset保存到RDB文件中,当前的RDB存储的数据内容和复制信息是一致性的可通过redis-check-rdb命令查看。如下图所示:

第二步：重启后加载RDB文件中的复制信息

redis加载RDB文件，会专门处理文件中辅助字段(AUX fields）信息，把其中repl_id和repl_offset加载到实例中，分别赋给master_replid和master_repl_offset两个变量值，特别注意当从库开启了AOF持久化，redis加载顺序发生变化优先加载AOF文件，但是由于aof文件中没有复制信息，所以导致重启后从实例依旧使用全量复制！

第三步：向主库上报复制信息，判断是否进行部分同步

从实例向主库上报master_replid和master_repl_offset+1；从实例同时满足以下两条件，就可以部分重新同步，否则执行全量同步：

从实例上报master_replid串，与主实例的master_replid1或replid2有一个相等，用于判断主从未发生改变；

从实例上报的master_repl_offset+1字节，还存在于主实例的复制积压缓冲区中，用于判断从库丢失部分是否在复制缓冲区中；
```

# 警告

```bash
WARNING: The TCP backlog setting of 511 cannot be enforced because /proc/sys/net/core/somaxconn is set to the lower value of 128.

echo net.core.somaxconn=511 >> /etc/sysctl.conf

WARNING overcommit_memory is set to 0! Background save may fail under low memory condition. To fix this issue add 'vm.overcommit_memory = 1' to /etc/sysctl.conf and then reboot or run the command 'sysctl vm.overcommit_memory=1' for this to take effect

echo vm.overcommit_memory=1 >> /etc/sysctl.conf
# 使/etc/sysctl.conf生效
sysctl -p
```

## 哨兵

# 附录

[redis-4.0.x中如何解决redis重启runid变化引起的全量复制](https://www.jianshu.com/p/54dabc470eb6)

[Redis4.0新特性(三)-PSYNC2_老叶茶馆的技术博客_51CTO博客](https://blog.51cto.com/imysql/3232665)

<aside>
💡

SLAVEOF 等效 REPLICAOF  （Version 5.0.0）

</aside>
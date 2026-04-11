---
title: Zookeeper
date: 2026-04-07
tags: [基础设施, Zookeeper]
type: note
status: complete
---

# Zookeeper

## **四种节点类型**

PERSISTI   PERSIST_SEQUENTIAL   EPHEMERAL    EPHEMERAL_SEQUENTIAL

（1）PERSIST：永久节点，会被持久化到磁盘之中。即使zookeeper重启之后，节点还是会存在。

（2）EPHEMERAL：瞬时节点，zookeeper重启之后，不会存在。假设Client没有了userheart了，这个节点也不会存在。Client与zookeeper的session结束了，这个节点也不会有了

（3）SEQUENTIAL:顺序节点。假设不是顺序节点的话，Client1创建了节点/a，那么其它Client就不能再创建节点/a，否则将报错。但是如果是SEQUENTIAL节点的话，Client1创建了节点/a，若其它Client也创建节点/a，则不会报错，也可以创建成功，只不过命名规则会在末尾加序号，越早创建的序号靠前。

## **可注册Watch操作**

为什么要注册Watch操作呢？因为我们其实希望Client可以感知到节点的各种更新操作，但是也不喜欢定时去查看这种变化，而是去订阅这种变化，每当节点有变化时，把这信息发给客户端。

（1）创建节点

（2）删除节点

（3）修改节点

（4）子节点的相关操作

## 选举方式

### **抢注leader节点——非公平模式**

这种方式和java里多线程的方式一样，谁抢到了资源就是谁的。这里同理，无论先后，谁抢到leader就是谁的。

（1）创建leader父节点，例如/kafka，并将其设置成persist节点。

（2）各客户端通过/kafka下创建leader节点，如/kafka/leader，来竞争leader节点，并且这个节点应该设置成ephemeral。

（3）若创建leader节点成功，则该客户端成功竞选为leader

（4）若创建leader节点失败，则竞选leader失败。而此时，则会在/kafka/leader节点上注册exist的watch，一旦后期这个leader被删除了，则可通过watch获取到信息，去竞争leader。

（5）leader可通过删除leader节点来放弃leader

（6）如果leader宕机了，因为leader节点被设置成ephemeral了，所以leader节点会自行删除。而由于其它follower已经在leader上注册了watch，所以会得到leader被删除的消息，参与leader竞选，从而保证总有客户端已leader角色工作。

### **先到先得，后者监视前者——公平模式**

（1）创建Leader父节点，如 /kafka，并将其设置为persist节点

（2）各客户端通过在/kafka下创建Leader节点，如/kafka/leader，来竞争Leader。该节点应被设置为ephemeral_sequential

（3）客户端通过getChildren方法获取/kakfa/下所有子节点，如果其注册的节点的id在所有子节点中最小，则当前客户端竞选Leader成功

（4）否则，在前面一个节点上注册watch，一旦前者被删除，则它得到通知，返回step 

（3）（并不能直接认为自己成为新Leader，因为可能前面的节点只是宕机了）

（5）Leader节点可通过自行删除自己创建的节点以放弃Leader

# 数据模型

节点类型与特性

### 持久节点

数据节点会一直存储在 ZooKeeper 服务器上，即使创建该节点的客户端与服务端的会话关闭了，该节点依然不会被删除。如果我们想删除持久节点，就要显式调用 delete 函数进行删除操作。

### 临时节点

所谓临时性是指，如果将节点创建为临时节点，那么该节点数据不会一直存储在 ZooKeeper 服务器上。当创建该临时节点的客户端会话因超时或发生异常而关闭时，该节点也相应在 ZooKeeper 服务器上被删除。同样，我们可以像删除持久节点一样主动删除临时节点。

### 有序节点

所谓节点有序是说在我们创建有序节点的时候，ZooKeeper 服务器会自动使用一个单调递增的数字作为后缀，追加到我们创建节点的后边。

# ACL权限控制

# Watch监控
# kubelet

kubelet：节点服务进程

> 作用：运行在每台节点上的后台服务，管理 Pod 生命周期
> 
- 每台机器（master/worker）上都需要运行 kubelet
- 负责：
    - 启动、监控、停止容器（配合 container runtime，如 containerd）
    - 汇报节点状态给 apiserver
    - 拉取镜像、探活、执行命令等

📌 kubelet **不会自己创建 Pod**，它是根据 apiserver 的指令来“执行”的。
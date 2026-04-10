# 镜像清空（Image prune）

### 定期清理

```bash
#-f  强制删除（不确认）

# 删除**没有标签**（<none>）的镜像{悬空镜像}
docker image prune

# 删除所有**未被使用**的镜像（包括有标签的）
docker image prune -a

# 清理所有未使用的镜像、容器、网络等（风险高，清理彻底）
docker system prune -a  
```

# `<none>`镜像

在 Docker 中，**`<none>` 镜像** 是一个统称，指代所有未正确打标签的镜像，它们可能包含多种不同类型的情况。

```bash
# 列出所有 <none> 镜像
docker images --format "{{.ID}}\t{{.Repository}}\t{{.Tag}}" | grep "<none>"

# 检查某个镜像是否被引用（如 abcd1234）
docker image <image_label> abcd1234 --format='{{.RepoTags}} {{.RepoDigests}}'

# 删除所有 <none> 镜像（危险！！！！）
docker rmi $(docker images --format "{{.ID}}\t{{.Repository}}" | grep "<none>" | awk '{print $1}')
```

---

# **`<none>` 镜像的常见类型**

### **悬空镜像（Dangling Images）**

通常是构建过程中生成的临时镜像，或删除镜像后残留的无用层（显示为 **`<none>:<none>`**

```bash
# 列出所有悬空镜像（未被任何容器引用的中间层或废弃的镜像层）。
docker images -f "dangling=true"
docker image ls --filter "dangling=true"

# 删除悬空镜像
docker image prune
```

### **中间层镜像（Intermediate Layers）**

### **被覆盖的旧镜像**

当给镜像重新打相同的标签时（如重复执行 **`docker build -t myimage:latest`**），旧镜像会变成 **`<none>`**
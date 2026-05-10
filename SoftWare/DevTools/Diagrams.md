---
title: Diagrams
date: 2026-04-07
tags:
  - 软件工具
  - 开发工具
type: note
status: complete
source: "https://mp.weixin.qq.com/s/BeBERUS0psHjoojZmwuJoA"
---

## Diagrams

> Diagrams 是一个用 Python 代码绘制云架构图的工具，可在版本控制中跟踪架构图变更。

### 核心概念

- 使用 Python DSL 描述云架构节点和连接（而非拖拽式 GUI）
- 支持 AWS / Azure / GCP / K8s / 通用 onprem 图标，以及 C4 模型
- 输出 SVG / PNG / PDF

### 使用示例

```python
from diagrams import Diagram
from diagrams.aws.compute import EC2
from diagrams.aws.database import RDS
from diagrams.aws.network import ELB

with Diagram("Web Service", show=False):
    ELB("lb") >> EC2("web") >> RDS("db")
```

### 优势

- 架构图作为代码，支持 Git diff / review
- 图标库丰富，风格统一
- 易于 CI/CD 集成，自动生成最新架构图

### 注意事项

- 本身不提供实时监控 / 自动发现功能，节点仍需手动声明
- 复杂拓扑需要额外组织代码结构

### 参考

- [GitHub: mingrammer/diagrams](https://github.com/mingrammer/diagrams)
- 相关：[[Mermaid]] [[Diagrams]]
# AGENTS.md

This is an **Obsidian vault** - a personal knowledge management directory, NOT a software project.

## Repository Type

- **Obsidian vault** with markdown notes
- No build scripts, tests, or development workflows
- `.obsidian/` contains Obsidian configuration (plugins, themes, workspace)

## Directory Structure

| Path | Purpose |
|------|---------|
| `Infrastructure/` | 基础设施 - Docker, Kubernetes, Linux, Database (MySQL/PostgreSQL/SQLite/MongoDB), Nginx, Redis, Queue, CDC, Cloud 等 |
| `Language/` | 编程语言 - Golang, Python, PHP, Shell, Lua, PromQL, Protocol Buffers, TOML, Makefile, Node, 汇编 等 |
| `Algorithm/` | 算法与数据结构 - 设计模式, leetcode, 奇门遁甲, AI大模型 |
| `Theory/` | 理论知识 - 网络, 加密, 安全, 人工智能, 画图, 字符集编码, 并发, CAP, Software License |
| `Microservices/` | 微服务 - 服务治理, 分布式中间件, DevOps, 微服务演进 |
| `SoftWare/` | 软件工具 - Git, Zsh, Wireshark, Proxy, DevTools, MacTools, CURL, Notes 等 |
| `Measurement/` | 行测资料 - 言语理解, 资料分析, 判断推理, 申论, 常识, 数量关系, 综合知识 |
| `Thooh/` | 个人技术笔记 - RPM打包, Nmap, GitHub, 错误码 |
| `WebClipper/` | 网页收集 - 使用 Obsidian Web Clipper 保存的网页笔记 |
| `Other/` | 其他 - 项目管理, 公司, 历史归档, 微信, 数据 |
| `Excalidraw/` | Excalidraw 图表文件 |
| `.obsidian/` | Obsidian 配置 |

## Notes for Agents

- This vault is read/write for personal note-taking
- No developer commands exist - no `npm`, `docker`, `build`, `test` workflows
- If future code is added to this repo, create separate AGENTS.md in that code directory
- Obsidian vault sync may be handled by `obsidian-git` plugin (see `.obsidian/plugins/obsidian-git/`)

# pipx

`pipx` 是一个 Python 命令行工具，用来 **安装和管理独立 Python 命令行应用**，它的核心特点是：

---

## 🔹 pipx 是什么

- 安装 Python CLI 工具（如 `langgraph-cli`、`black`、`httpie`）
- **每个 CLI 工具都会在独立的虚拟环境里安装**
- 全局可用命令行，不污染系统 Python 或其他项目的依赖
- 支持指定 Python 版本来创建环境

---

## 🔹 优势

| 特点 | 说明 |
| --- | --- |
| 隔离依赖 | 每个工具单独 venv，不会影响系统 Python |
| 全局可用 | 命令行直接执行，无需激活 venv |
| 可管理 | pipx 可以升级、卸载或重装 CLI 工具 |
| 指定 Python 版本 | 用 `--python /path/to/python` 安装时指定 Python 解释器 |

---

## 🔹 基本用法

### 1. 安装 pipx（macOS）

```bash
brew install pipx
pipx ensurepath

```

> ensurepath 会把 pipx 的可执行文件路径加入 shell PATH。
> 

### 2. 安装 CLI 工具

```bash
pipx install langgraph-cli
# 或者带可选功能
pipx install "langgraph-cli[inmem]"

```

### 3. 指定 Python 版本安装 CLI

```bash
pipx install "langgraph-cli[inmem]" --python /usr/local/opt/python@3.13/bin/python3.13

```

### 4. 使用 CLI

```bash
langgraph new ~/projects/my-app --template new-langgraph-project-python
langgraph dev

```

### 5. 管理安装的 CLI

```bash
pipx list          # 查看已安装工具
pipx upgrade langgraph-cli
pipx uninstall langgraph-cli

```

---
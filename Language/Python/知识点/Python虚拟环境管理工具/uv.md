# uv

### **官网地址** https://docs.astral.sh/uv/‌

uv是一个python虚拟环境管理工具，可以用来替代pip、pyenv、virtualenv等等工具。根据官网的介绍，使用uv来管理虚拟环境，相比于pip能得到至少10倍以上的性能提升。

uv工具有如下功能：

- 管理python版本；
- 管理第三方库（Python packages）的版本；
- 拥有全局的第三方库的缓存，能减少磁盘空间占用；
- 安装uv不需要python环境，可以通过curl或pip安装；
- 多平台支持：macOS、Linux、Windows;

### **安装命令**‌：

- ‌Linux/‌macOS

```css
curl -LsSf https://astral.sh/uv/install.sh | sh
# 或者wget（效果一样）
wget -qO- https://astral.sh/uv/install.sh | sh
```

### 命令

```bash
# 验证安装
uv --version

# 卸载
uv cache clean
rm -r "$(uv python dir)"
rm -r "$(uv tool dir)"

# mac/linux
rm ~/.local/bin/uv ~/.local/bin/uvx
# windows
rm $HOME.local\bin\uv.exe
rm $HOME.local\bin\uvx.exe

# 列表
uv python list

# 安装
uv python install 3.12
uv python install pypy@3.10

# 查找
uv python find 3.10

# 使用
uv python pin 版本号

# 创建虚拟环境
uv init # 初始化
uv venv --python=cpython-3.13.11 --seed

# 安装pip
python -m ensurepip --upgrade
python -m pip install --upgrade pip setuptools wheel

# 安装依赖
python -m pip install -e .

# 同步依赖
uv sync

# 添加依赖
uv add 依赖项=版本号

# 运行程序
uv run 程序文件名 [命令行参数]
uv run python -m pip xxx

# uvx
uvx python main.py
# 等价于
uv run main.py
# 等价于
uv tool run main.py

```

langgrph

```bash
pipx install "langgraph-cli[inmem]" --python /usr/local/opt/python@3.13/bin/python3.13

```

打包&解包

```latex
uv build 

python -m pip install ./dist/platform-mcp-0.1.0-py3-none-any.whl
```
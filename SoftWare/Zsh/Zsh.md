---
title: Zsh
date: 2026-04-07
tags: [软件工具, Zsh]
type: note
status: complete
---

# Zsh

```bash
# 查看当前使用的 shell
echo $SHELL

# 查看安装的 shell
cat /etc/shells

# 使用 brew 更新 zsh
brew install zsh

# 切换为 zsh
chsh -s /bin/zsh

# 执行从 oh-my-zsh 的 GitHub 下载的安装脚本
sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"

# 中文乱码问题
vi ~/.zshrc
	export LC_ALL=en_US.UTF-8  
	export LANG=en_US.UTF-8
source ~/.zshrc
```

## zsh config

```bash
#启用命令纠错功能
ENABLE_CORRECTION="true"
```

[[Zsh/plugins]]
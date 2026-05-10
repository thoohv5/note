---
title: asdf
date: 2026-04-07
tags:
  - 软件工具
  - 开发工具
type: guide
status: complete
---

## asdf

### 安装

```bash
git clone https://github.com/asdf-vm/asdf.git ~/.asdf --branch v0.14.0
echo '. "$HOME/.asdf/asdf.sh"' >> ~/.bashrc
echo '. "$HOME/.asdf/completions/asdf.bash"' >> ~/.bashrc
source ~/.bashrc
```

### Go插件

```bash
asdf plugin add golang https://github.com/asdf-community/asdf-golang.git
```

### PHP插件

```bash
asdf plugin add php https://github.com/asdf-community/asdf-php.git

PHP_CONFIGURE_OPTIONS="--with-openssl=$(brew --prefix openssl) --with-iconv=$(brew --prefix libiconv)" asdf install php 8.2.29

export PHP_CONFIGURE_OPTIONS="--with-openssl=$(brew --prefix openssl@1.1) --with-iconv=$(brew --prefix libiconv)"
asdf install php 7.2.34

```

### Python 插件

```bash
asdf plugin add python https://github.com/danhper/asdf-python.git
```

### 全局

```bash
asdf set php 8.2.29 -p

asdf reshim php

asdf current
```

- `p` 表示 **全局（permanent）**
- `l` 表示 **本地（local）**，相当于 `asdf local`
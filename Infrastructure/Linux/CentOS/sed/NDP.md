---
title: NDP
date: 2026-04-07
tags: [基础设施, Linux, CentOS]
type: note
status: complete
source: https://www.bbsmax.com/A/Ae5Re0YLJQ/
---

## NDP

## 多行匹配命令

N 将下一行加入到模式空间

D 删除模式空间中的第一个字符到第一个换行符

P 打印模式空间中的第一个字符到第一个换行符

### 示例

```bash
# 将偶数行合并到奇数行
sed 'N;s/\n/ /' file.txt

# 删除空行后的下一行
sed '/^$/{N;/^\n$/d}' file.txt

# 打印匹配行的下一行
sed -n '/pattern/{N;p}' file.txt
```

### 参考

- [sed 之 N D P](https://www.bbsmax.com/A/Ae5Re0YLJQ/)
- [[sed]]
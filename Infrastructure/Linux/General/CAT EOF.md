---
title: CAT EOF
date: 2026-04-07
  - 基础设施
  - Linux
type: note
status: complete
---

## CAT EOF

## 覆盖（>）

```bash
cat <<EOF > file

file content
EOF
```

```bash
cat > file <<EOF

file content
EOF
```

## 追加（>>）

```bash
cat <<EOF >> file

file content
EOF
```

```bash
cat >> file <<EOF

file content
EOF
```

## EOF 说明

| 写法 | 含义 |
| --- | --- |
| `<<EOF` | 正常 here-doc，**会进行变量替换、命令替换** |
| `<<'EOF'` | 引号包围的 delimiter，**不会做变量替换或命令替换**（纯文本） |
| `<<-EOF` | 会 **忽略每行前面的 tab 字符**，常用于格式整齐的缩进 |
| `<<-'EOF'` | 组合写法：**不会替换变量**，同时 **忽略开头的 tab 缩进** |

### `<<EOF`（变量会被替换）：

```bash
cat <<EOF
Hello $NAME
EOF
## 输出: Hello world
```

---

### `<<'EOF'`（变量不会被替换）：

```bash
cat <<'EOF'
Hello $NAME
EOF
## 输出: Hello $NAME
```

---

### `<<-EOF`（可缩进 with tab）：

```bash
cat <<-EOF
	This is tab-indented
EOF
## 输出: This is tab-indented
```

注意：**只有 tab 会被忽略，空格不会！**

---

### `<<-'EOF'`（不替换变量 + 忽略 tab 缩进）：

结合上面两个行为，适合写一些配置模板又想格式美观时用。

```bash
cat <<-'EOF'
	Hello $NAME
EOF
## 输出: Hello $NAME
```
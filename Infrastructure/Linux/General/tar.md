---
title: tar
date: 2026-04-07
  - 基础设施
  - Linux
type: note
status: incomplete
---

## tar

```sql
-c: 建立压缩档案
-x：解压
-t：查看内容
-r：向压缩归档文件末尾追加文件
-u：更新原压缩包中的文件
```

```sql
## 解压
tar -zxvf xx.tar.gz
tar -zjvf xx.tar.bz2
tar -Zxvf xx.tar.Z
unrar e xx.rar
unzip xx.zip

tar -zxvf xx.tar.gz -C /tmp

## 压缩
tar -cvf xx.tar *.jpg
tar -zcvf xx.tar.gz *.jpg
tar -jcvf xx.tar.bz2 *.jpg
tar -Zcvf xx.tar.Z *.jpg
rar a xx.rar *.jpg
zip xx.zip *.jpg

```
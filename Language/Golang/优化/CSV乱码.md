---
title: CSV乱码
date: 2026-04-07
tags:
  - 编程语言
  - Golang
type: note
status: incomplete
---

## CSV乱码

### csv to go

```bash
"golang.org/x/text/encoding/simplifiedchinese"
"golang.org/x/text/transform"

reader := transform.NewReader(bytes.NewReader(s), simplifiedchinese.GBK.NewDecoder())
```

### go to csv

```bash
buf := new(bytes.Buffer)
// BOM头，解决excel乱码问题
buf.WriteString("\xEF\xBB\xBF")
w := csv.NewWriter(buf)
```
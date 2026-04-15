---
title: 字符串(string)
date: 2026-04-07
tags: [编程语言, Golang]
type: guide
status: complete
---

# 字符串(string)

1. 双引号
2. 反引号

双引号中的转义字符被替换，而反引号中转义字符被原样输出。

```bash
bytes.Buffer 
strings.Builder
strings.HasPrefix
strings.HasSuffix
strings.Split
strings.Index
strings.Join
strings.Replace
strings.ToUpper
strings.ToLower
strings.Count
strings.Contains
```

```bash
	var str = "Yes我爱我家！"

	for i, v := range []byte(str) {
		fmt.Printf("(%d, %X) ", i, v)
	}
	fmt.Println()

	// i 不连续
	for i, v := range str {
		fmt.Printf("(%d, %c) ", i, v)
	}
	fmt.Println()

	// 推荐
	for i, v := range []rune(str) {
		fmt.Printf("(%d, %c) ", i, v)
	}
	fmt.Println()

	byteLen := len(str)                    // 字节长度
	charLen := utf8.RuneCountInString(str) //字符长度

	fmt.Println(byteLen, charLen)

(0, 59) (1, 65) (2, 73) (3, E6) (4, 88) (5, 91) (6, E7) (7, 88) (8, B1) (9, E6) (10, 88) (11, 91) (12, E5) (13, AE) (14, B6) (15, EF) (16, BC) (17, 81) 
(0, Y) (1, e) (2, s) (3, 我) (6, 爱) (9, 我) (12, 家) (15, ！) 
(0, Y) (1, e) (2, s) (3, 我) (4, 爱) (5, 我) (6, 家) (7, ！) 
18 8
```
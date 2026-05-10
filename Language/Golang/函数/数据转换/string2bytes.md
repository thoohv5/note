---
title: string2bytes
date: 2026-04-07
tags: [编程语言, Golang, 函数]
type: note
status: complete
---

## string2bytes

```bash
## go 1.6

// StringToBytes converts string to byte slice without a memory allocation.
func StringToBytes(s string) (b []byte) {
   sh := *(*reflect.StringHeader)(unsafe.Pointer(&s))
   bh := (*reflect.SliceHeader)(unsafe.Pointer(&b))
   bh.Data, bh.Len, bh.Cap = sh.Data, sh.Len, sh.Len
   return b
}

// BytesToString converts byte slice to string without a memory allocation.
func BytesToString(b []byte) string {
   return *(*string)(unsafe.Pointer(&b))
}
```

```bash
## go 1.7

// StringToBytes converts string to byte slice without a memory allocation.
func StringToBytes(s string) (b []byte) {

   return *(*[]byte)(unsafe.Pointer(
      &struct {
         string
         Cap int
      }{s, len(s)},
   ))
}
```
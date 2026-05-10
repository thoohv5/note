---
title: ip2int
date: 2026-04-07
tags:
  - 编程语言
  - Golang
type: note
status: complete
---

## ip2int

```bash
package main
 
import (
	"bytes"
	"fmt"
	"strconv"
	"strings"
)
 
func main() {
	fmt.Println(StringIpToInt("172.16.1.1"))
	fmt.Println(StringIpToInt("172.16.2.9"))
	fmt.Println(IpIntToString(2886729986))
}
 
func StringIpToInt(ipstring string) int {
	ipSegs := strings.Split(ipstring, ".")
	var ipInt int = 0
	var pos uint = 24
	for _, ipSeg := range ipSegs {
		tempInt, _ := strconv.Atoi(ipSeg)
		tempInt = tempInt << pos
		ipInt = ipInt | tempInt
		pos -= 8
	}
	return ipInt
}
 
// int转ip串
func IpIntToString(ipInt int) string {
	ipSegs := make([]string, 4)
	var len int = len(ipSegs)
	buffer := bytes.NewBufferString("")
	for i := 0; i < len; i++ {
		tempInt := ipInt & 0xFF
		ipSegs[len-i-1] = strconv.Itoa(tempInt)
		ipInt = ipInt >> 8
	}
	for i := 0; i < len; i++ {
		buffer.WriteString(ipSegs[i])
		if i < len-1 {
			buffer.WriteString(".")
		}
	}
	return buffer.String()
}
```
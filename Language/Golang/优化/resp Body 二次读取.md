---
title: resp.Body 二次读取
date: 2026-04-07
tags: [编程语言, Golang, 优化]
type: note
status: incomplete
---

## resp.Body 二次读取

```bash
		// 读取请求
		if bodyByte, err = ioutil.ReadAll(resp.Body); nil != err {
			return fmt.Errorf("request read err, bodyByte: %v, %w", bodyByte, err)
		}
		r.response.Body = ioutil.NopCloser(bytes.NewBuffer(bodyByte))
```
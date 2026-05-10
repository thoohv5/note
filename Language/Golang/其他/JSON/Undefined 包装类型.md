---
title: Undefined 包装类型
date: 2026-04-07
tags: [编程语言, Golang, 其他]
type: note
status: complete
---

## Undefined 包装类型

思路：利用结构体“零值”+`omitzero` 标签。

```go
type Undefined[T any] struct {  
    Val     T   // 实际值  
    Present bool// 标记字段是否出现  
}

func (u *Undefined[T]) UnmarshalJSON(data []byte) error {  
    if err := json.Unmarshal(data, &u.Val); err != nil {  
        return fmt.Errorf("Undefined: 反序列化失败: %w", err)  
    }  
    u.Present = true  
    return nil  
}  

func (u Undefined[T]) MarshalJSON() ([]byte, error) {  
    data, err := json.Marshal(u.Val)  
    if err != nil {  
        return nil, fmt.Errorf("Undefined: 序列化失败: %w", err)  
    }  
    return data, nil  
}  

// 供 encoding/json 判断零值  
func (u Undefined[T]) IsZero() bool {  
    return !u.Present  
}
```
---
title: Callable
date: 2026-04-07
  - 编程语言
  - Python
type: note
status: incomplete
---

## Callable

### 普通函数

```python
def greet(name: str) -> str:
    return f"Hello {name}"

## greet 是 Callable，可以传给 Agent
tools = [greet]
```

### 类实现 **call**

```python
class Greeter:
    def __call__(self, name: str) -> str:
        return f"Hello {name}"

greeter = Greeter()
tools = [greeter]  # greeter 也是 Callable
```
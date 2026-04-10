---
title: @dataclass
date: 2026-04-07
tags: [编程语言, Python]
type: note
status: complete
---

# @dataclass

- `@dataclass` 的核心作用就是 **自动生成构造函数、比较函数、打印函数**
- 还可以结合 **`frozen`、`order`、`default_factory`、`__post_init__`** 实现更多功能
- 本质上，dataclass 的代码可以完全手写，只是 `@dataclass` 帮你自动生成，减少重复代码

### 原始 dataclass 定义

```python
from dataclasses import dataclass

@dataclass
class Member:
    name: str
    role: str = "Developer"

# 等价于
class Member:
    def __init__(self, name: str, role: str = "Developer") -> None:
        self.name = name
        self.role = role

    def __repr__(self) -> str:
        return f"Member(name={self.name!r}, role={self.role!r})"

    def __eq__(self, other) -> bool:
        if other.__class__ is self.__class__:
            return self.name == other.name and self.role == other.role
        return NotImplemented

    def __hash__(self) -> int:
        return hash((self.name, self.role))
```

### 参数c`frozen=True`

```python
@dataclass(frozen=True)
class Point:
    x: int
    y: int

# 等价于

class Point:
    __slots__ = ('x', 'y')  # 避免动态增加属性

    def __init__(self, x: int, y: int) -> None:
        object.__setattr__(self, 'x', x)
        object.__setattr__(self, 'y', y)

    def __repr__(self) -> str:
        return f"Point(x={self.x!r}, y={self.y!r})"

    def __eq__(self, other) -> bool:
        if other.__class__ is self.__class__:
            return self.x == other.x and self.y == other.y
        return NotImplemented

    def __hash__(self) -> int:
        return hash((self.x, self.y))

    def __setattr__(self, key, value):
        # 冻结属性，禁止修改
        raise AttributeError(f"{self.__class__.__name__} is frozen")
```

### 方法`__post_init__` 会被调用在 `__init__` 之后

```python
@dataclass
class Team:
    name: str
    members: list
    size: int = 0

    def __post_init__(self):
        self.size = len(self.members)

# 等价于
class Team:
    def __init__(self, name, members):
        self.name = name
        self.members = members
        self.size = 0
        self.__post_init__()  # 自动调用

    def __post_init__(self):
        self.size = len(self.members)
```
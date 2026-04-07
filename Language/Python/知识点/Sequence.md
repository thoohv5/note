# Sequence

在 Python 类型提示里，**`Sequence`** 是一个泛型类型，表示 **有顺序的容器**，可以通过索引访问元素，常见的类型有 **`list`、`tuple`、`str`**（字符串也算序列，但在这里不用）。

---

### 基本用法

```python
from typing import Sequence

def greet_all(names: Sequence[str]):
    for name in names:
        print(f"Hello {name}")

greet_all(["Alice", "Bob"])  # ✅ list
greet_all(("Charlie", "Dana"))  # ✅ tuple
```

- **`Sequence[str]`** 表示这个序列里的元素都是 `str`。
- **可以使用索引**，也可以用 `for` 循环遍历。
- **不能保证可变性**，如果你需要可修改的列表，用 `list[str]`。
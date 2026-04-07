# Pydantic

# 基本概念

**BaseModel 是 Pydantic 的核心数据类，用于：**

- 数据验证（Validation）
- 类型转换（Type Coercion）
- 数据序列化/反序列化（Serialization）
- 默认值管理
- 结构化数据模型（类似 TypeScript interface）
- 错误自动处理（ValidationError）

### 示例

```python
from pydantic import BaseModel, Field, validator
from typing import List

# -----------------------------
# 嵌套模型
# -----------------------------
class Item(BaseModel):
    name: str = Field(..., min_length=3, pattern=r"^[A-Za-z]+$") # 验证
    quantity: int = 1  # 默认数量 1
    quantityx: int | None = None # 可选字段

class Order(BaseModel):
    order_id: int
    items: List[Item] = Field(default_factory=list)
    customer: str
    priority: bool = False

    # -----------------------------
    # 自定义验证器
    # -----------------------------
    @validator("items")
    def check_items_not_empty(cls, v):
        if len(v) == 0:
            raise ValueError("Order must have at least one item")
        return v

    @validator("customer")
    def customer_name_not_empty(cls, v):
        if not v.strip():
            raise ValueError("Customer name cannot be empty")
        return v

# -----------------------------
# 使用示例
# -----------------------------
if __name__ == "__main__":
    # 正确创建
    order1 = Order(
        order_id=1001,
        items=[{"name": "Apple", "quantity": 3}, {"name": "Banana"}],
        customer="Alice",
    )

    print("Order as dict:", order1.dict())
    print("Order as JSON:", order1.json())

    # 默认值示例
    print("Default priority:", order1.priority)
    print("Default quantity for Banana:", order1.items[1].quantity)

    # 错误示例触发验证器
    try:
        order2 = Order(order_id=1002, items=[], customer="  ")
    except Exception as e:
        print("Validation Error:", e)

```
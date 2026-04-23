---
title: 方式（way）
date: 2026-04-07
  - 理论
  - AI
type: guide
status: incomplete
---

## 方式（way）

### 使用代理（**With agents**）

```python
import os
from langchain_openai import ChatOpenAI

os.environ["OPENAI_API_KEY"] = "sk-..."

model = ChatOpenAI(model="gpt-4.1")
```

### 独立运行（**Standalone**）

```python
import os
from langchain.chat_models import init_chat_model

os.environ["OPENAI_API_KEY"] = "sk-..."

model = init_chat_model("gpt-4.1")
```
---
title: Message
date: 2026-04-07
tags: [理论, AI, 框架]
type: note
status: incomplete
---

## Message

```python
messages = [
    {"role": "system", "content": "You are a poetry expert"},
    {"role": "user", "content": "Write a haiku about spring"},
    {"role": "assistant", "content": "Cherry blossoms bloom..."}
]
response = model.invoke(messages)
```
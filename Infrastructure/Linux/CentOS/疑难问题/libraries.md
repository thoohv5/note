---
title: libraries
date: 2026-04-07
tags:
  - 基础设施
  - Linux
type: note
status: incomplete
---

## libraries

问题

```jsx
error while loading shared libraries: libatomic.so.1: cannot open shared object file: No such file or directory
```

解决办法

```jsx
yum -y install libatomic
```
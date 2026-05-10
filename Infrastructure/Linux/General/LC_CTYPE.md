---
title: LC_CTYPE
date: 2026-04-07
tags: [基础设施, Linux, General]
type: note
status: incomplete
---

## LC_CTYPE

```bash
-bash: warning: setlocale: LC_CTYPE: cannot change locale (UTF-8): No such file or directory
```

```bash
cat <<EOF >> /etc/locale.conf
LC_ALL=en_US.utf8
LC_CTYPE=en_US.utf8
LANG=en_US.utf8
EOF

```
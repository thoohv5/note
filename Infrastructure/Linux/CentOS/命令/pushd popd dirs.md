---
title: pushd/popd/dirs*
date: 2026-04-07
  - 基础设施
  - Linux
type: note
status: complete
---

## pushd/popd/dirs*

```bash
pushd
popd
dirs -v
```

```bash
are added to the list with the pushd command; the popd command removes entries from the list.
              +n     Displays the nth entry counting from the left of the list shown by dirs when invoked without options, starting with zero.
              -n     Displays the nth entry counting from the right of the list shown by dirs when invoked without options, starting with zero.
              -c     Clears the directory stack by deleting all of the entries.
              -l     Produces a longer listing; the default listing format uses a tilde to denote the home directory.
              -p     Print the directory stack with one entry per line.
              -v     Print the directory stack with one entry per line, prefixing each entry with its index in the stack.

              The return value is 0 unless an invalid option is supplied or n indexes beyond the end of the directory stack.
```
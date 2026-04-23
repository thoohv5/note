---
title: Go Get
date: 2026-04-07
  - 编程语言
  - Golang
type: reference
status: complete
---

## Go Get

### **Why does "go get" use HTTPS when cloning a repository?**

Companies often permit outgoing traffic only on the standard TCP ports 80 (HTTP) and 443 (HTTPS), blocking outgoing traffic on other ports, including TCP port 9418 (git) and TCP port 22 (SSH). When using HTTPS instead of HTTP, `git` enforces certificate validation by default, providing protection against man-in-the-middle, eavesdropping and tampering attacks. The `go get` command therefore uses HTTPS for safety.

`Git` can be configured to authenticate over HTTPS or to use SSH in place of HTTPS. To authenticate over HTTPS, you can add a line to the `$HOME/.netrc` file that git consults:

```
machine github.com login USERNAME password APIKEY
```

For GitHub accounts, the password can be a [personal access token](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/).

`Git` can also be configured to use SSH in place of HTTPS for URLs matching a given prefix. For example, to use SSH for all GitHub access, add these lines to your `~/.gitconfig`:

```
[url "ssh://git@github.com/"]
	insteadOf = https://github.com/
```
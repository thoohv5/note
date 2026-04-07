# 工作区模式（Workspace mode）

# 背景

在项目开发的时候不仅仅需要使用别人开发的开源模块，还有自己公司内部项目。比如 sso module 等。

**使用 module 开发模块的弊端**

在使用 golang 的 module 来开发模块的时候需要在项目的`go.mod`文件中引入对应的项目，但是golang 默认会去相对应的地址去拉去对应的包，但是这个时候我们的module 并没有提交到自己的仓库中。那么这个时候golang 就会报错，找不到对应的 package。

做法就是在 `go.mod` 文件中添加一条指令 `replace` ，涉及修改mod文件。

# 概述

针对 mo module 的问题，Michael Matloob 提出了 Workspace Mode（工作区模式）。相关 issue 讨论：[cmd/go: add a workspace mode](https://github.com/golang/go/issues/45713) ， [[https://go.googlesource.com/proposal/+/master/design/45713-workspace]]，感兴趣的可以去参阅。

想使用 go work，那基本的要求就是你的 golang version 必须是 `golang 1.18` 以上的版本

## 命令

```bash
❯ go help work
Work provides access to operations on workspaces.

Note that support for workspaces is built into many other commands, not
just 'go work'.

See 'go help modules' for information about Go's module system of which
workspaces are a part.

See https://go.dev/ref/mod#workspaces for an in-depth reference on
workspaces.

See https://go.dev/doc/tutorial/workspaces for an introductory
tutorial on workspaces.

A workspace is specified by a go.work file that specifies a set of
module directories with the "use" directive. These modules are used as
root modules by the go command for builds and related operations.  A
workspace that does not specify modules to be used cannot be used to do
builds from local modules.

go.work files are line-oriented. Each line holds a single directive,
made up of a keyword followed by arguments. For example:

	go 1.18

	use ../foo/bar
	use ./baz

	replace example.com/foo v1.2.3 => example.com/bar v1.4.5

The leading keyword can be factored out of adjacent lines to create a block,
like in Go imports.

	use (
	  ../foo/bar
	  ./baz
	)

The use directive specifies a module to be included in the workspace's
set of main modules. The argument to the use directive is the directory
containing the module's go.mod file.

The go directive specifies the version of Go the file was written at. It
is possible there may be future changes in the semantics of workspaces
that could be controlled by this version, but for now the version
specified has no effect.

The replace directive has the same syntax as the replace directive in a
go.mod file and takes precedence over replaces in go.mod files.  It is
primarily intended to override conflicting replaces in different workspace
modules.

To determine whether the go command is operating in workspace mode, use
the "go env GOWORK" command. This will specify the workspace file being
used.

Usage:

	go work <command> [arguments]

The commands are:

	edit        edit go.work from tools or scripts
	init        initialize workspace file
	sync        sync workspace build list to modules
	use         add modules to workspace file
	vendor      make vendored copy of dependencies

Use "go help work <command>" for more information about a command.
```

### 初始化

```bash
go work init example mypkg
```
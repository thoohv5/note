---
title: 抽象语法树(AST)
date: 2026-04-07
tags: [编程语言, Golang, 语法]
type: note
status: complete
---

## 抽象语法树(AST)

## 简介

### AST（Abstract Syntax Tree）

根据维基百科的介绍：在计算机科学中，**抽象语法树(AST)**，或者仅仅是**语法树**，是用编程语言编写的源代码的抽象语法结构的树状表示。树的每个节点都表示源代码中出现的一个构造。

大多数编译器和解释器都使用AST作为源代码的内部表示，AST通常会省略语法树中的分号、换行字符、白空格、大括号、方括号和圆括号等。

### 生成步骤

词法分析器(Lexar)对文本(Source Code)进行词法分析，生成Token。一般接下来是将它传给一个解析器，然后检索生成AST。

### Golang中的AST

### 官方包

- go/scanner 词法解析，将源代码分割成一个个token
- go/token token类型及相关结构体定义
- go/ast ast的结构定义
- go/parser 语法分析，读取token流

Golang的AST主要由三种节点构成：
`表达式和类型节点(Expressions and type nodes)语句节点(statement nodes)声明节点(declaration nodes)`

所有的AST节点都实现`ast.Node`，同时也可能实现以下接口

- ast.Expr - 代表表达式和类型的节点
- ast.Stmt - 代表报表节点
- ast.Decl - 代表声明节点

```
// All node types implement the Node interface.
type Node interface {
	Pos() token.Pos // position of first character belonging to the node
	End() token.Pos // position of first character immediately after the node
}

// All expression nodes implement the Expr interface.
type Expr interface {
	Node
	exprNode()
}

// All statement nodes implement the Stmt interface.
type Stmt interface {
	Node
	stmtNode()
}

// All declaration nodes implement the Decl interface.
type Decl interface {
	Node
	declNode()
}

```

### 解析

```go
package main

import (
    "fmt"
    "go/ast"
    "go/parser"
    "go/token"
)

var srcCode = `
package hello

import "fmt"

func greet() {
    var msg = "Hello World!"
    fmt.Println(msg)
}
`

func main() {
    fset := token.NewFileSet()
    f, err := parser.ParseFile(fset, "", srcCode, 0)
    if err != nil {
        fmt.Printf("err = %s", err)
    }

	// Print the AST.
	ast.Print(fset, f)
    ast.Inspect(f, func(n ast.Node) bool {
        // Called recursively.
        ast.Print(fset, n)
        return true
    })

}

```

解析结果

```go
0  *ast.File {
1  .  Package: 2:1
2  .  Name: *ast.Ident {
3  .  .  NamePos: 2:9
4  .  .  Name: "hello"
5  .  }
6  .  Decls: []ast.Decl (len = 2) {
7  .  .  0: *ast.GenDecl {
8  .  .  .  TokPos: 4:1
9  .  .  .  Tok: import
10  .  .  .  Lparen: -
11  .  .  .  Specs: []ast.Spec (len = 1) {
12  .  .  .  .  0: *ast.ImportSpec {
13  .  .  .  .  .  Path: *ast.BasicLit {
14  .  .  .  .  .  .  ValuePos: 4:8
15  .  .  .  .  .  .  Kind: STRING
16  .  .  .  .  .  .  Value: "\\"fmt\\""
17  .  .  .  .  .  }
18  .  .  .  .  .  EndPos: -
19  .  .  .  .  }
20  .  .  .  }
21  .  .  .  Rparen: -
22  .  .  }
23  .  .  1: *ast.FuncDecl {
24  .  .  .  Name: *ast.Ident {
25  .  .  .  .  NamePos: 6:6
26  .  .  .  .  Name: "greet"
27  .  .  .  .  Obj: *ast.Object {
28  .  .  .  .  .  Kind: func
29  .  .  .  .  .  Name: "greet"
30  .  .  .  .  .  Decl: *(obj @ 23)
31  .  .  .  .  }
32  .  .  .  }
33  .  .  .  Type: *ast.FuncType {
34  .  .  .  .  Func: 6:1
35  .  .  .  .  Params: *ast.FieldList {
36  .  .  .  .  .  Opening: 6:11
37  .  .  .  .  .  Closing: 6:12
38  .  .  .  .  }
39  .  .  .  }
40  .  .  .  Body: *ast.BlockStmt {
41  .  .  .  .  Lbrace: 6:14
42  .  .  .  .  List: []ast.Stmt (len = 2) {
43  .  .  .  .  .  0: *ast.DeclStmt {
44  .  .  .  .  .  .  Decl: *ast.GenDecl {
45  .  .  .  .  .  .  .  TokPos: 7:4
46  .  .  .  .  .  .  .  Tok: var
47  .  .  .  .  .  .  .  Lparen: -
48  .  .  .  .  .  .  .  Specs: []ast.Spec (len = 1) {
49  .  .  .  .  .  .  .  .  0: *ast.ValueSpec {
50  .  .  .  .  .  .  .  .  .  Names: []*ast.Ident (len = 1) {
51  .  .  .  .  .  .  .  .  .  .  0: *ast.Ident {
52  .  .  .  .  .  .  .  .  .  .  .  NamePos: 7:8
53  .  .  .  .  .  .  .  .  .  .  .  Name: "msg"
54  .  .  .  .  .  .  .  .  .  .  .  Obj: *ast.Object {
55  .  .  .  .  .  .  .  .  .  .  .  .  Kind: var
56  .  .  .  .  .  .  .  .  .  .  .  .  Name: "msg"
57  .  .  .  .  .  .  .  .  .  .  .  .  Decl: *(obj @ 49)
58  .  .  .  .  .  .  .  .  .  .  .  .  Data: 0
59  .  .  .  .  .  .  .  .  .  .  .  }
60  .  .  .  .  .  .  .  .  .  .  }
61  .  .  .  .  .  .  .  .  .  }
62  .  .  .  .  .  .  .  .  .  Values: []ast.Expr (len = 1) {
63  .  .  .  .  .  .  .  .  .  .  0: *ast.BasicLit {
64  .  .  .  .  .  .  .  .  .  .  .  ValuePos: 7:14
65  .  .  .  .  .  .  .  .  .  .  .  Kind: STRING
66  .  .  .  .  .  .  .  .  .  .  .  Value: "\\"Hello World!\\""
67  .  .  .  .  .  .  .  .  .  .  }
68  .  .  .  .  .  .  .  .  .  }
69  .  .  .  .  .  .  .  .  }
70  .  .  .  .  .  .  .  }
71  .  .  .  .  .  .  .  Rparen: -
72  .  .  .  .  .  .  }
73  .  .  .  .  .  }
74  .  .  .  .  .  1: *ast.ExprStmt {
75  .  .  .  .  .  .  X: *ast.CallExpr {
76  .  .  .  .  .  .  .  Fun: *ast.SelectorExpr {
77  .  .  .  .  .  .  .  .  X: *ast.Ident {
78  .  .  .  .  .  .  .  .  .  NamePos: 8:2
79  .  .  .  .  .  .  .  .  .  Name: "fmt"
80  .  .  .  .  .  .  .  .  }
81  .  .  .  .  .  .  .  .  Sel: *ast.Ident {
82  .  .  .  .  .  .  .  .  .  NamePos: 8:6
83  .  .  .  .  .  .  .  .  .  Name: "Println"
84  .  .  .  .  .  .  .  .  }
85  .  .  .  .  .  .  .  }
86  .  .  .  .  .  .  .  Lparen: 8:13
87  .  .  .  .  .  .  .  Args: []ast.Expr (len = 1) {
88  .  .  .  .  .  .  .  .  0: *ast.Ident {
89  .  .  .  .  .  .  .  .  .  NamePos: 8:14
90  .  .  .  .  .  .  .  .  .  Name: "msg"
91  .  .  .  .  .  .  .  .  .  Obj: *(obj @ 54)
92  .  .  .  .  .  .  .  .  }
93  .  .  .  .  .  .  .  }
94  .  .  .  .  .  .  .  Ellipsis: -
95  .  .  .  .  .  .  .  Rparen: 8:17
96  .  .  .  .  .  .  }
97  .  .  .  .  .  }
98  .  .  .  .  }
99  .  .  .  .  Rbrace: 9:1
100  .  .  .  }
101  .  .  }
102  .  }
103  .  Scope: *ast.Scope {
104  .  .  Objects: map[string]*ast.Object (len = 1) {
105  .  .  .  "greet": *(obj @ 27)
106  .  .  }
107  .  }
108  .  Imports: []*ast.ImportSpec (len = 1) {
109  .  .  0: *(obj @ 12)
110  .  }
111  .  Unresolved: []*ast.Ident (len = 1) {
112  .  .  0: *(obj @ 77)
113  .  }
114  }

```

### ast.File

所有AST节点的根。值实现了`ast.Node`接口

![](https://raw.githubusercontent.com/thoohv5/ob/main/picture202312281726146.png)

`ast.File` 有引用 `ast.Ident(包名)`、`ast.GenDecl(导入声明)`、`ast.FuncDecl(函数声明)`

![](https://raw.githubusercontent.com/thoohv5/ob/main/picture202312281708541.png)

## 附录

[https://yuroyoro.github.io/goast-viewer/index.html](https://yuroyoro.github.io/goast-viewer/index.html)

[https://www.cnblogs.com/double12gzh/p/13632267.html](https://www.cnblogs.com/double12gzh/p/13632267.html)
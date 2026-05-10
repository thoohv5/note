---
title: Cobra CLI 框架
date: 2026-04-07
tags: [编程语言, Golang, 第三方包]
type: reference
status: complete
source: https://github.com/spf13/cobra
---

# Cobra CLI 框架

## 概念
Cobra 是 Go 语言命令行框架，Kubernetes/Hugo/GitHub CLI 等均基于此。

44k+ stars。

## 安装
go get -u github.com/spf13/cobra@latest
# 脚手架工具
go install github.com/spf13/cobra-cli@latest

## 核心概念
APPNAME VERB NOUN --ADJECTIVE（像句子一样可读的 CLI）
- Command：动作
- Args：对象
- Flags：修饰符

## 特性
- 子命令、嵌套子命令
- POSIX 兼容 flags（长短格式）
- 智能建议（app srver → app server?）
- 自动生成 help/man page
- Shell 自动补全（bash/zsh/fish/powershell）

## 基础示例
var rootCmd = &cobra.Command{
    Use:   "app",
    Short: "A brief description",
    Run: func(cmd *cobra.Command, args []string) { },
}
func init() {
    rootCmd.Flags().StringP("config", "c", "", "config file")
}
func main() { rootCmd.Execute() }

## 场景
Go CLI 工具开发，运维脚本封装，微服务管理工具

## 注意事项
- 与 viper 配合可实现 12-factor 配置管理
- cobra-cli 可快速生成项目脚手架
- flag 由 pflag 库提供，兼容标准 flag 接口

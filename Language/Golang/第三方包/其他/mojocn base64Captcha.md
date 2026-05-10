---
title: mojocn/base64Captcha
date: 2026-04-07
tags: [编程语言, Golang, 第三方包]
type: reference
status: complete
source: https://github.com/mojocn/base64Captcha
---

# mojocn/base64Captcha

## 概念
base64Captcha 是 Go 语言验证码库，生成 Base64 编码的图片/音频验证码，支持数字、字符串、中文、数学运算等类型。

## 安装
go get -u github.com/mojocn/base64Captcha

## 核心接口
- Driver：生成验证码内容（DrawCaptcha/GenerateIdQuestionAnswer）
- Store：存储验证码答案（Set/Get/Verify），内置内存和 sync.Map 实现
- Item：Base64 编码输出

## 内置 Driver
| Driver | 说明 |
|--------|------|
| DriverDigit | 数字验证码 |
| DriverString | 字母数字混合 |
| DriverMath | 数学运算 |
| DriverChinese | 中文验证码 |
| DriverAudio | 音频验证码 |

## 示例
c := base64Captcha.NewCaptcha(driver, store)
id, b64s, err := c.Generate()  // 返回 id 和 base64 图片
match := store.Verify(id, answer, true)

## 场景
Web 登录验证码，防止机器人注册/登录，支持多语言。

## 注意事项
- Store 需自行实现（Redis/Etcd 等）用于分布式场景
- 验证后应 clear=true 防止重用
- 内置字体支持中英文

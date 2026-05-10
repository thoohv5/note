---
title: 方法（Key methods）
date: 2026-04-07
tags: [理论, AI, 框架]
type: note
status: complete
---

## 方法（Key methods）

**model  模型**

**stringrequired细绳必需的**

The name or identifier of the specific model you want to use with a provider. You can also specify both the model and its provider in a single argument using the ’:’ format, for example, ‘openai:o1’.您要与提供程序一起使用的特定模型的名称或标识符。您也可以使用“:”格式在单个参数中同时指定模型及其提供程序，例如“openai:o1”。

**api_key**

**string  细绳**

The key required for authenticating with the model’s provider. This is usually issued when you sign up for access to the model. Often accessed by setting an用于向模型提供商进行身份验证的密钥。通常在您注册访问模型时颁发。通常通过设置来访问。environment variable  环境变量.

**temperature  温度**

**number  数字**

Controls the randomness of the model’s output. A higher number makes responses more creative; lower ones make them more deterministic.控制模型输出的随机性。数值越高，响应越具创造性；数值越低，响应越具确定性。

**max_tokens  最大令牌数**

**number  数字**

Limits the total number of限制总数tokens  代币 in the response, effectively controlling how long the output can be.在响应中，有效地控制输出的长度。

**timeout  暂停**

**number  数字**

The maximum time (in seconds) to wait for a response from the model before canceling the request.等待模型响应的最长时间（以秒为单位），超过此时间将取消请求。

**max_retries  最大重试次数**

**number  数字**

The maximum number of attempts the system will make to resend a request if it fails due to issues like network timeouts or rate limits.如果由于网络超时或速率限制等问题导致请求失败，系统将尝试重新发送请求的最大次数。
---
title: HTTP传输大文件
date: 2026-04-07
  - 基础设施
  - Nginx
type: guide
status: complete
---

## HTTP传输大文件

### 数据压缩

```sql
Accept-Encoding
Content-Encoding
```

### 工作原理

1. 首先浏览器（也就是客户端）发送请求时，通过Accept-Encoding带上自己支持的内容编码格式列表；
2. 服务端在接收到请求后，从中挑选出一种用来对响应信息进行编码，并通过Content-Encoding来说明服务端选定的编码信息
3. 浏览器在拿到响应正文后，依据Content-Encoding进行解压。

### 数据分块传输

```sql
Tranfer-Encodeing:chunked

## 与上面互斥
Content-Length:
```

- 每个分块包含两部分，长度头和数据块。
- 长度头是以CRLF(回车换行，即\r\n)结尾的一行明文，用16进制数字表示长度。
- 数据块紧跟其后，最后同样使用CRLF结尾，但数据不包含CRLF。
- 最后用一个长度为0的块（即最后的空行）来表示结束，即"0\r\n\r\n"。

### 范围请求

```sql
Accept-Ranges: bytes
```

### **服务器收到Range字段后的响应过程**

1. 它必须检查范围是否合法，比如文件只有 100 个字节，但请求“200-300”，这就是范围越界了。服务器就会返回状态码 416，意思是“你的范围请求有误，我无法处理，请再检查一下”。
2. 如果范围正确，服务器就可以根据 Range 头计算偏移量，读取文件的片段了，返回状态码“206 Partial Content”，和 200 的意思差不多，但表示 body 只是原数据的一部分。
3. 服务器要添加一个响应头字段 Content-Range，告诉片段的实际偏移量和资源的总大小，格式是“bytes x-y/length”，与 Range 头区别在没有“=”，范围后多了总长度。例如，对于“0-10”的范围请求，值就是“bytes 0-10/100”。
4. 最后剩下的就是发送数据了，直接把片段用 TCP 发给客户端，一个范围请求就算是处理完了。
范围请求

### 断点续传

1. 先发个 HEAD，看服务器是否支持范围请求，同时获取文件的大小；
2. 开 N 个线程，每个线程使用 Range 字段划分出各自负责下载的片段，发请求传输数据；
3. 下载意外中断也不怕，不必重头再来一遍，只要根据上次的下载记录，用 Range 请求剩下的那一部分就可以了。

### 多段数据

```sql
multipart/byteranges
```

## 附录

[HTTP 协议中的Content-Encoding_枫秀天涯的博客-CSDN博客_content-encoding](https://blog.csdn.net/u014569188/article/details/78912446)
---
title: ntlm
date: 2026-04-07
tags: [软件工具]
type: guide
status: complete
---

# ntlm

NTLM is a [challenge–response authentication protocol](https://en.wikipedia.org/wiki/Challenge)93response_authentication which uses three messages to authenticate a client in a connection-oriented environment (connectionless is similar), and a fourth additional message if integrity is desired.[[5]](https://en.wikipedia.org/wiki/NTLM#cite_note-5)[[6]](https://en.wikipedia.org/wiki/NTLM#cite_note-6)[[7]](https://en.wikipedia.org/wiki/NTLM#cite_note-7)[[8]](https://en.wikipedia.org/wiki/NTLM#cite_note-8)NTLM 是一种质询-响应身份验证协议，它使用三条消息在面向连接的环境中对客户端进行身份验证（无连接类似），如果需要完整性，则使用第四条附加消息。 [[5]](https://en.wikipedia.org/wiki/NTLM#cite_note-5) [[6]](https://en.wikipedia.org/wiki/NTLM#cite_note-6) [[7]](https://en.wikipedia.org/wiki/NTLM#cite_note-7) [[8]](https://en.wikipedia.org/wiki/NTLM#cite_note-8)

1. First, the client establishes a network path to the server and sends a NEGOTIATE_MESSAGE advertising its capabilities.
    
    [[9]](https://en.wikipedia.org/wiki/NTLM#cite_note-9)
    
    首先，客户端建立到服务器的网络路径，并发送通告其功能的NEGOTIATE_MESSAGE。 [[9]](https://en.wikipedia.org/wiki/NTLM#cite_note-9)
    
2. Next, the server responds with CHALLENGE_MESSAGE which is used to establish the identity of the client.
    
    [[10]](https://en.wikipedia.org/wiki/NTLM#cite_note-10)
    
    接下来，服务器使用用于建立客户端标识的CHALLENGE_MESSAGE进行响应。 [[10]](https://en.wikipedia.org/wiki/NTLM#cite_note-10)
    
3. Finally, the client responds to the challenge with an AUTHENTICATE_MESSAGE.
    
    [[11]](https://en.wikipedia.org/wiki/NTLM#cite_note-11)
    
    最后，客户以AUTHENTICATE_MESSAGE回应挑战。
    

## 分类

### LM hash

### NTLM hash

### NTLMv1

### NTLMv2
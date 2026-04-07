# Client

http.Client 默认使用开启keepalive，默认使用长连接

如果需要关闭长连接

`DisableKeepAlives: true` 和 `"Connection": "close"` 都可以用来关闭 HTTP 长连接，但是它们有一些细微的区别：

1. **DisableKeepAlives: true**:
    - 这是一个针对整个 `http.Transport` 的设置，用于禁用所有 HTTP 请求的长连接。
    - 当设置了 `DisableKeepAlives: true` 时，每个 HTTP 请求都会创建一个新的连接，但连接在请求结束后不会立即关闭，而是留在连接池中供将来重用。
    - 这种方法在发送多个请求时效率更高，因为可以避免重复地建立和关闭连接。
    - 适用于需要频繁进行 HTTP 请求的场景，但不需要保持长连接的情况。
2. **"Connection": "close"**:
    - 这是在每个单独的 HTTP 请求中设置的标头，用于指示服务器在发送响应后关闭连接。
    - 当在请求标头中包含 `"Connection": "close"` 时，服务器会在发送完响应后关闭连接，而不会重用该连接。
    - 这种方法适用于特定的请求，您可以选择性地关闭某些请求的连接，而保持其他请求的连接活动。
    - 当您只需要关闭特定请求的连接，而不是禁用整个连接池时，这是一个更精确的控制方式。

总之，`DisableKeepAlives: true` 用于禁用所有请求的长连接，而 `"Connection": "close"` 用于在单个请求中关闭连接。您可以根据您的需求选择适合您的方法。
# resp.Body 二次读取

```bash
		// 读取请求
		if bodyByte, err = ioutil.ReadAll(resp.Body); nil != err {
			return fmt.Errorf("request read err, bodyByte: %v, %w", bodyByte, err)
		}
		r.response.Body = ioutil.NopCloser(bytes.NewBuffer(bodyByte))
```
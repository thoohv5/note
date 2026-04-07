# HTTP

```go
package main

import (
	"context"
	"fmt"
	"io"
	"io/ioutil"
	"net"
	"net/http"
	"time"
)

func main() {

	timeOut := 3 * time.Second
	url := "https://wwww.baidu.com"
	maxPerHost := 5

	timeoutContext, _ := context.WithTimeout(context.Background(), timeOut)
	req, err := http.NewRequestWithContext(timeoutContext, "GET", url, nil)
	if err != nil {
		panic(err)
	}
	resp, err := httpClientWithMaxPerlHost(maxPerHost).Do(req)
	if err != nil {
		panic(err)
	}
	defer resp.Body.Close()

	if resp.StatusCode != 200 {
		io.Copy(ioutil.Discard, resp.Body)
		panic(err)
	}

	bytes, err := ioutil.ReadAll(resp.Body)
	if err != nil {
		panic(err)
	}

	fmt.Println(string(bytes))
}

func httpClientWithMaxPerlHost(maxPerlHostConnect int) *http.Client {
	return &http.Client{
		Transport: &http.Transport{
			Proxy: http.ProxyFromEnvironment,
			DialContext: (&net.Dialer{
				Timeout:   5 * time.Second,
				KeepAlive: 30 * time.Second,
			}).DialContext,
			// MaxIdleConns:          maxPerlHostConnect*10,
			IdleConnTimeout:       90 * time.Second,
			TLSHandshakeTimeout:   10 * time.Second,
			ExpectContinueTimeout: 1 * time.Second,
			ResponseHeaderTimeout: 30 * time.Second,
			MaxConnsPerHost:       maxPerlHostConnect,
			MaxIdleConnsPerHost:   maxPerlHostConnect / 2,
		},
	}
}
```

# 附录

[Golang 产生大量TIME_WAIT或ESTABLISHED的问题_go语言找'tcp'连接中存在多个'time_wait_男人如花花易落的博客-CSDN博客](https://blog.csdn.net/sinat_36436112/article/details/118698978)
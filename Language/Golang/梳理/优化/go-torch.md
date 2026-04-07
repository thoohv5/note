# go-torch

## go-torch is deprecated, use pprof instead

As of Go 1.11, flamegraph visualizations are available in `go tool pprof` directly!

```
# This will listen on :8081 and open a browser.
# Change :8081 to a port of your choice.
$ go tool pprof -http=":8081" [binary] [profile]

```

If you cannot use Go 1.11, you can get the latest `pprof` tool and use it instead:

```
# Get the pprof tool directly
$ go get -u github.com/google/pprof

$ pprof -http=":8081" [binary] [profile]

```

# 附录

[https://github.com/uber-archive/go-torch](https://github.com/uber-archive/go-torch)
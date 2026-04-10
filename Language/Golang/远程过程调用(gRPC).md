---
title: 远程过程调用(gRPC)
date: 2026-04-07
tags: [编程语言, Golang]
type: note
status: complete
---

# 远程过程调用(gRPC)

[gRPC](https://www.grpc.io/)

## Protocol Buffers

[Developer Guide | Protocol Buffers | Google Developers](https://developers.google.com/protocol-buffers/docs/overview)

# [golang](https://github.com/golang)/[**protobuf**](https://github.com/golang/protobuf)

[golang/protobuf](https://github.com/golang/protobuf)

## protoc-gen-bin

```sql
GIT_TAG="v1.4.3" # change as needed
go get -d -u github.com/golang/protobuf/protoc-gen-go
git -C "$(go env GOPATH)"/src/github.com/golang/protobuf checkout $GIT_TAG
go install github.com/golang/protobuf/protoc-gen-go

GIT_TAG="v1.4.3" # change as needed
go get -d -u github.com/golang/protobuf/protoc-gen-go
git -C "/Users/thooh/Workspace/go"/src/github.com/golang/protobuf checkout $GIT_TAG
go install github.com/golang/protobuf/protoc-gen-go

GIT_TAG="v1.3.2" # change as needed
go get -d -u github.com/golang/protobuf/protoc-gen-go
git -C "/Users/thooh/Workspace/go"/src/github.com/golang/protobuf checkout $GIT_TAG
go install github.com/golang/protobuf/protoc-gen-go
```

# [gogo](https://github.com/gogo)/[**protobuf**](https://github.com/gogo/protobuf)

[gogo/protobuf](https://github.com/gogo/protobuf)

## Protobuf

[protocolbuffers/protobuf](https://github.com/google/protobuf)

```bash
In the downloads section of each release, you can find pre-built binaries in zip packages: protoc-$VERSION-$PLATFORM.zip. It contains the protoc binary as well as a set of standard .proto files distributed along with protobuf.

protoc "$file" -I "$path" --go_out=plugins=grpc:"$out"
```

## Protoc-go-inject-tag

[favadi/protoc-go-inject-tag](https://github.com/favadi/protoc-go-inject-tag)

```bash
protoc-go-inject-tag -input="$pb_file" >>"$debug" 2>&1
```

## Grpc-gateway

[grpc-ecosystem/grpc-gateway](https://github.com/grpc-ecosystem/grpc-gateway)

```bash
protoc "$file" -I "$path" --grpc-gateway_out=logtostderr=true:"$out"
```
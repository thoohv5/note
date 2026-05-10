---
title: Graceful Shutdown机制
date: 2026-04-07
tags:
  - 编程语言
  - Golang
type: note
status: complete
---

## Graceful Shutdown机制

```go
package main

import (
	"context"
	"errors"
	"log"
	"net/http"
	"os"
	"os/signal"
	"syscall"
	"time"
)

func main() {
	mux := http.NewServeMux()
	mux.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
		w.Write([]byte("Hello, World!"))
	})

	server := &http.Server{
		Addr:         ":8787",
		Handler:      mux,
		ReadTimeout:  15 * time.Second,
		WriteTimeout: 15 * time.Second,
		IdleTimeout:  60 * time.Second,
	}

	serverError := make(chan error, 1)
	go func() {
		log.Printf("服务运行中: http://localhost%s", server.Addr)
		if err := server.ListenAndServe(); !errors.Is(err, http.ErrServerClosed) {
			serverError <- err
		}
	}()

	stop := make(chan os.Signal, 1)
	signal.Notify(stop, os.Interrupt, syscall.SIGTERM)

	select {
	case err := <-serverError:
		log.Printf("服务错误: %v", err)
	case sig := <-stop:
		log.Printf("收到关机信号: %v", sig)
	}

	log.Println("服务正在关闭...")
	ctx, cancel := context.WithTimeout(context.Background(), 15*time.Second)
	defer cancel()

	if err := server.Shutdown(ctx); err != nil {
		log.Printf("服务关闭错误: %v", err)
		return
	}
	log.Println("服务已正常退出")
}

```
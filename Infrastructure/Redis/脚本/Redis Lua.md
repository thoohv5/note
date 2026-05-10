---
title: Redis Lua
date: 2026-04-07
tags:
  - 基础设施
  - Redis
  - 脚本
type: note
status: complete
---

## Redis Lua

```go
package lua

import (
	"bytes"
	"context"
	"embed"
	"fmt"
	"github.com/redis/go-redis/v9"
	"strings"
	"sync"
)

//go:embed scripts/*.lua
var luaScriptFS embed.FS

type luaScripts struct {
	rdb redis.Scripter
	m   map[string]string
	rwm sync.RWMutex
}

type ILuaScripts interface {
	Load(ctx context.Context, name string) (string, error)
	Preload(ctx context.Context, names ...string) (err error)
	Execute(ctx context.Context, name string, keys, args []string, callback func(ctx context.Context, ret *redis.Cmd) error) error
}

func NewLuaScripts(rdb redis.Scripter) ILuaScripts {
	return &luaScripts{
		rdb: rdb,
		m:   make(map[string]string),
	}
}
func (l *luaScripts) Load(ctx context.Context, name string) (string, error) {
	sn, err := l.getScriptContent(name)
	if err != nil {
		return "", err
	}
	result, err := l.rdb.ScriptLoad(ctx, sn).Result()
	if err != nil {
		return "", fmt.Errorf("load script %q: %w", name, err)
	}
	return result, nil
}

func (l *luaScripts) Preload(ctx context.Context, names ...string) (err error) {
	if len(names) == 0 {
		names, err = l.listScriptNames()
		if err != nil {
			return err
		}
	}
	l.rwm.Lock()
	defer l.rwm.Unlock()
	for _, name := range names {
		result, err := l.Load(ctx, name)
		if err != nil {
			return err
		}
		l.m[name] = result
	}
	return
}

func (l *luaScripts) Execute(ctx context.Context, name string, keys, args []string, callback func(ctx context.Context, ret *redis.Cmd) error) (err error) {
	l.rwm.RLock()
	script, ok := l.m[name]
	l.rwm.RUnlock()
	if !ok {
		l.rwm.RLock()
		script, ok = l.m[name]
		l.rwm.RUnlock()
		if !ok {
			script, err = l.Load(ctx, name)
			if err != nil {
				return
			}
			l.rwm.Lock()
			l.m[name] = script
			l.rwm.Unlock()
		}
	}
	rc := l.rdb.EvalSha(ctx, script, keys, args)
	if err = rc.Err(); err != nil {
		// 处理NOSCRIPT错误（脚本可能被清除）
		if strings.Contains(err.Error(), "NOSCRIPT") {
			// 清除缓存并重试
			l.rwm.Lock()
			delete(l.m, name)
			l.rwm.Unlock()
			return l.Execute(ctx, name, keys, args, callback)
		}
		return fmt.Errorf("eval script %q: %w", name, err)
	}
	if err := callback(ctx, rc); err != nil {
		return fmt.Errorf("exec script callback %q: %w", name, err)
	}
	return nil
}

func (l *luaScripts) getScriptContent(name string) (string, error) {
	bs, err := luaScriptFS.ReadFile("scripts/" + name)
	if err != nil {
		return "", fmt.Errorf("luaScriptFS.ReadFile: %w", err)
	}
	return bytes.NewBuffer(bs).String(), nil
}

func (l *luaScripts) listScriptNames() ([]string, error) {
	entries, err := luaScriptFS.ReadDir("scripts")
	if err != nil {
		return nil, fmt.Errorf("luaScriptFS.ReadDir: %w", err)
	}
	var names []string
	for _, entry := range entries {
		if !entry.IsDir() {
			names = append(names, entry.Name())
		}
	}
	return names, nil
}

```
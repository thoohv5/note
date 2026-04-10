---
title: 有限状态机（finite-state machine，FSM）
date: 2026-04-07
tags: [编程语言, Golang]
type: reference
status: complete
---

# 有限状态机（finite-state machine，FSM）

# 概述

有限状态机（FSM）是管理复杂状态流转的优雅工具，其核心在于通过明确定义**状态**、**事件**和**转换规则**，将业务逻辑模块化

## 特征如下：

- 状态（state）个数是有限的。
- 任意一个时刻，只处于其中一种状态。
- 某种条件下（触发某种 event），会从一种状态转变（transition）为另一种状态。

满足以上三个特征的对象，我们都可以称其为有限状态机。

- 结合EventBus
    
    ```go
    package main
    
    import (
    	"fmt"
    	
    	"github.com/asaskevich/EventBus"
    	"github.com/looplab/fsm"
    )
    
    func main() {
    	// 初始化 EventBus
    	bus := EventBus.New()
    	
    	// 创建 FSM
    	fsm := fsm.NewFSM(
    		"idle", // 初始状态
    		fsm.Events{
    			{Name: "start", Src: []string{"idle"}, Dst: "running"},
    			{Name: "stop", Src: []string{"running"}, Dst: "idle"},
    		},
    		fsm.Callbacks{
    			"enter_state": func(e *fsm.Event) { 
    				bus.Publish("state_change", e.FSM.Current()) 
    			},
    		},
    	)
    	
    	// 订阅状态变化事件
    	bus.Subscribe("state_change", func(state string) {
    		fmt.Printf("状态已改变为: %s\n", state)
    	})
    	
    	// 订阅其他事件并触发状态转换
    	bus.Subscribe("user_command", func(cmd string) {
    		switch cmd {
    		case "start":
    			fsm.Event("start")
    		case "stop":
    			fsm.Event("stop")
    		}
    	})
    	
    	// 发布事件
    	bus.Publish("user_command", "start")
    	bus.Publish("user_command", "stop")
    }
    ```
    

## **使用场景建议**

- 当需要严格的状态管理和明确的转换规则时，优先考虑 FSM
- 当需要松耦合的组件间通信时，使用 EventBus
- 对于复杂应用，可以结合两者：FSM 管理核心业务逻辑状态，EventBus 处理UI/服务间通信

# 附录

[https://github.com/looplab/fsm](https://github.com/looplab/fsm)
# Go + Redis

**核心思路**
1. **存储结构**：使用 Redis 的 **Sorted Set（ZSet）**，以任务的 **执行时间戳** 作为分数（Score），任务内容作为成员（Member）。
2. **消费者逻辑**：消费者定期扫描 ZSet 中到期的任务（分数小于当前时间戳），取出并执行。
3. **原子性操作**：通过 `ZRANGEBYSCORE` 和 `ZREM` 的原子操作（Lua 脚本）保证任务只被消费一次。

```go
package main

import (
	"context"
	"fmt"
	"time"
	"github.com/go-redis/redis/v8"
)

var rdb *redis.Client

func init() {
	rdb = redis.NewClient(&redis.Options{
		Addr:     "localhost:6379",
		Password: "",
		DB:       0,
	})
}

// AddDelayTask 添加延时任务
func AddDelayTask(ctx context.Context, taskID string, delay time.Duration) error {
	// 计算执行时间戳（当前时间 + 延时时间）
	executeTime := time.Now().Add(delay).Unix()
	// 添加到 ZSet
	return rdb.ZAdd(ctx, "delay_tasks", &redis.Z{
		Score:  float64(executeTime),
		Member: taskID,
	}).Err()
}

func main() {
	ctx := context.Background()
	// 添加一个 10 秒后执行的任务
	err := AddDelayTask(ctx, "task_123", 10*time.Second)
	if err != nil {
		fmt.Println("添加任务失败:", err)
	}
}

// 消费者逻辑
func StartConsumer(ctx context.Context) {
	for {
		// 获取当前时间戳
		now := time.Now().Unix()
		// 查询所有到期的任务（分数 <= 当前时间）
		tasks, err := rdb.ZRangeByScore(ctx, "delay_tasks", &redis.ZRangeBy{
			Min:    "0",
			Max:    fmt.Sprintf("%d", now),
			Offset: 0,
			Count:  10, // 一次最多取10个任务
		}).Result()

		if err != nil {
			fmt.Println("查询任务失败:", err)
			continue
		}

		if len(tasks) == 0 {
			// 无任务，休眠1秒
			time.Sleep(1 * time.Second)
			continue
		}

		// 使用 Lua 脚本原子化移除任务并返回任务列表
		luaScript := `
			local tasks = redis.call('ZRANGEBYSCORE', KEYS[1], '-inf', ARGV[1])
			if #tasks > 0 then
				redis.call('ZREM', KEYS[1], unpack(tasks))
			end
			return tasks
		`
		keys := []string{"delay_tasks"}
		vals := []interface{}{now}

		res, err := rdb.Eval(ctx, luaScript, keys, vals).Result()
		if err != nil {
			fmt.Println("执行Lua脚本失败:", err)
			continue
		}

		// 处理任务
		if res != nil {
			taskIDs := res.([]interface{})
			for _, taskID := range taskIDs {
				fmt.Printf("执行任务: %s\n", taskID)
				// TODO: 执行具体任务逻辑
			}
		}
	}
}

func main() {
	ctx := context.Background()
	go StartConsumer(ctx)
	select {} // 阻塞主协程
}
```
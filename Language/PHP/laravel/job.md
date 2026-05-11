---
title: job
date: 2026-04-07
tags: [编程语言, PHP, Laravel]
type: note
status: complete
---
## Laravel Job

> Laravel 队列任务 (Job) 用于异步处理耗时操作。

### 创建 Job

```bash
php artisan make:job ProcessPodcast
```

### Job 类定义

```php
class ProcessPodcast implements ShouldQueue
{
    use Dispatchable, InteractsWithQueue, Queueable, SerializesModels;

    public function handle(): void
    {
        // 业务逻辑
    }
}
```

### 分发

```php
ProcessPodcast::dispatch($podcast);
ProcessPodcast::dispatch()->onQueue('high');
ProcessPodcast::dispatch()->delay(now()->addMinutes(10));
```

### 队列配置

```env
QUEUE_CONNECTION=redis  # sync/database/redis/beanstalkd/sqs
```

### 常见方法

| 方法 | 说明 |
|------|------|
| `dispatch()` | 分发任务 |
| `dispatchSync()` | 同步执行 |
| `delay()` | 延迟执行 |
| `onQueue()` | 指定队列 |
| `tries` | 最大重试次数 |
| `retryUntil` | 超时退出 |
| `failed()` | 任务失败回调 |

### 参考

- [[Laravel 队列]]
- [[Redis Queue]]
- [[Beanstalkd]]

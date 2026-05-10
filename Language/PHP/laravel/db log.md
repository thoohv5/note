---
title: db log
date: 2026-04-07
tags:
  - 编程语言
  - PHP
type: note
status: complete
---

## db log

```bash
## Laravel 5.1
\DB::listen(function ($sql, $bindings, $time, $connection) {         
});

Laravel >5.1
\DB::listen(function (QueryExecuted $executed) use ($isProd) {
});
```

```bash
$isProd = app()->environment('production');
if (!$isProd || config('app.log_sql')) {
    \DB::listen(function (QueryExecuted $executed) use ($isProd) {
        if ($isProd && stripos($executed->sql, 'select') === 0) {
						// 生产环境不记录Select语句
            return;
        }
        $bindings = $executed->bindings ?: [];
        foreach ($bindings as &$binding) {
            if (is_string($binding)) {
                $binding = addslashes($binding);
                $binding = "'{$binding}'";
            }
        }
        unset($binding);

        if (strpos($executed->sql, '%s') !== false
            || strpos($executed->sql, '%d') !== false
            || strpos($executed->sql, '%f') !== false
        ) {
            $sql = sprintf('[Query] Time: %s, SQL: %s, Binds: %s', $executed->time, $executed->sql,
                implode(',', $bindings));
            //\Log::info($sql);
        } else {
            $params = array_merge([str_replace('?', '%s', $executed->sql)], $bindings);
            $sql = call_user_func_array('sprintf', $params);
            $sql = sprintf('[Query] Time: %s, SQL: %s', $executed->time, $sql);
            //\Log::info($sql);
        }

        $handler = new StreamHandler(storage_path('logs/sql-' . date('Y-m-d') . '.log'), Logger::DEBUG);
        $handler->setFormatter(new LineFormatter(null, null, true, true));
        $log = new Logger('sql');
        $log->pushHandler($handler);
        $log->addInfo($sql);
    });
}
```

```bash
DB::connection()->enableQueryLog();#开启执行日志
$result = DB::table('advert')->whereJsonContains('tag',"1")->get();
dd(DB::getQueryLog());
```
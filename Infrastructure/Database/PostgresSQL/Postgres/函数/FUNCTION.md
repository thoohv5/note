---
title: FUNCTION
date: 2026-04-07
  - 基础设施
  - 数据库
type: note
status: complete
---

## FUNCTION

```
CREATE [ OR REPLACE ] FUNCTION
    _`name`_ ( [ [ _`argmode`_ ] [ _`argname`_ ] _`argtype`_ [ { DEFAULT | = } _`default_expr`_ ] [, ...] ] )
    [ RETURNS _`rettype`_
      | RETURNS TABLE ( _`column_name`_ _`column_type`_ [, ...] ) ]
  { LANGUAGE _`lang_name`_
    | TRANSFORM { FOR TYPE _`type_name`_ } [, ... ]
    | WINDOW
    | IMMUTABLE | STABLE | VOLATILE | [ NOT ] LEAKPROOF
    | CALLED ON NULL INPUT | RETURNS NULL ON NULL INPUT | STRICT
    | [ EXTERNAL ] SECURITY INVOKER | [ EXTERNAL ] SECURITY DEFINER
    | PARALLEL { UNSAFE | RESTRICTED | SAFE }
    | COST _`execution_cost`_
    | ROWS _`result_rows`_
    | SUPPORT _`support_function`_
    | SET _`configuration_parameter`_ { TO _`value`_ | = _`value`_ | FROM CURRENT }
    | AS '_`definition`_'
    | AS '_`obj_file`_', '_`link_symbol`_'
  } ...

```

## 重载

指在同一个模式下定义具有相同名称但参数列表不同的多个函数。
函数重载主要基于函数的参数签名（参数数量、类型以及类型顺序）

建议使用

```
drop function
create or replace

代替

create or replace

```

### 打印

```
raise notice xx

```

## 示例

```

CREATE OR REPLACE FUNCTION increment(IN i integer) RETURNS integer AS $$
        BEGIN
                RETURN i + 1;
        END;
$$ LANGUAGE plpgsql;

```
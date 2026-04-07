# 公共表表达式（CTE）

# 概述

在 **MySQL** 中，公共表表达式（**Common Table Expression，简称 CTE**）是一种临时的结果集，可以在 `SELECT`、`INSERT`、`UPDATE` 或 `DELETE` 语句中引用，语法上使用 `WITH` 子句定义。

# 语法

```sql
WITH cte_name AS (
    SELECT ...
)
SELECT * FROM cte_name;
```

```sql
# 多个 CTE 联合使用
WITH cte1 AS (...),
     cte2 AS (...)
SELECT ...
FROM cte1
JOIN cte2 ON ...
```

# 示例

## 数据

```sql
CREATE TABLE employees (
  employee_id INT PRIMARY KEY AUTO_INCREMENT,
  first_name VARCHAR(50),
  last_name VARCHAR(50),
  email VARCHAR(100),
  phone_number VARCHAR(20),
  hire_date DATE,
  job_id VARCHAR(10),
  salary DECIMAL(10,2),
  commission_pct DECIMAL(5,2),
  manager_id INT,
  department_id INT
);
```

```sql
INSERT INTO employees (first_name, last_name, email, phone_number, hire_date, job_id, salary, commission_pct, manager_id, department_id)
SELECT 
    CONCAT('First', FLOOR(1 + (RAND() * 100))),  -- 随机生成 first_name
    CONCAT('Last', FLOOR(1 + (RAND() * 100))),   -- 随机生成 last_name
    CONCAT(LOWER(SUBSTRING('ABCDEFGHIJKLMNOPQRSTUVWXYZ', FLOOR(1 + (RAND() * 26)), 1)), '.', LOWER(SUBSTRING('ABCDEFGHIJKLMNOPQRSTUVWXYZ', FLOOR(1 + (RAND() * 26)), 1)), '@example.com'),  -- 随机生成 email
    CONCAT('+1-', FLOOR(100 + (RAND() * 900)), '-', FLOOR(100 + (RAND() * 900)), '-', FLOOR(1000 + (RAND() * 9000))),  -- 随机生成 phone_number
    CURDATE() - INTERVAL (FLOOR(RAND() * 3650) + 1) DAY,  -- 随机生成 hire_date，10年内
    CASE 
        WHEN RAND() < 0.2 THEN 'DEV'
        WHEN RAND() < 0.4 THEN 'QA'
        WHEN RAND() < 0.6 THEN 'HR'
        WHEN RAND() < 0.8 THEN 'PM'
        ELSE 'SALES' 
    END AS job_id,  -- 随机生成 job_id
    ROUND(3000 + (RAND() * 12000), 2),  -- 随机生成工资（3000 到 15000 之间）
    CASE 
        WHEN RAND() < 0.3 THEN ROUND(RAND() * 0.2, 2)  -- 随机生成 commission_pct（最多 20%）
        ELSE NULL 
    END AS commission_pct,
    FLOOR(1 + (RAND() * 10)) AS manager_id,  -- 随机生成 manager_id
    FLOOR(1 + (RAND() * 5))  -- 随机生成 department_id（1 到 5 之间）
FROM 
    information_schema.tables  -- 从 `information_schema` 表中选择数据
LIMIT 100;  -- 生成 100 条数据

```

---

## 非递归CTE

查询每个部门平均工资大于 10000 的员工信息

```sql
WITH avg_salary_by_dept AS (
  SELECT department_id, AVG(salary) AS avg_salary
  FROM employees
  GROUP BY department_id
)
SELECT e.*
FROM employees e
JOIN avg_salary_by_dept a
  ON e.department_id = a.department_id
WHERE a.avg_salary > 10000;
```

---

## 递归 CTE

找出某位经理下的所有下属（层级结构）：

```sql
WITH RECURSIVE employee_hierarchy AS (
  SELECT employee_id, first_name, last_name, manager_id
  FROM employees
  WHERE manager_id IS NULL  -- 顶级经理

  UNION ALL

  SELECT e.employee_id, e.first_name, e.last_name, e.manager_id
  FROM employees e
  JOIN employee_hierarchy h ON e.manager_id = h.employee_id
)
SELECT * FROM employee_hierarchy;
```
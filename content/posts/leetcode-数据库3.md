---
title: "leetcode-数据库3"
date: 2026-09-15T10:27:49+08:00
tags: ["leetcode", "数据库"]
draft: false
---


## 第N高的薪水

表: Employee

+-------------+------+
| Column Name | Type |
+-------------+------+
| id          | int  |
| salary      | int  |
+-------------+------+
id 是该表的主键（列中的值互不相同）。
该表的每一行都包含有关员工工资的信息。


编写一个解决方案查询 Employee 表中第 n 高的 不同 工资。如果少于 n 个不同工资，查询结果应该为 null 。

MySQL
```
CREATE FUNCTION getNthHighestSalary(N INT) RETURNS INT
BEGIN
DECLARE M INT;
    SET M = N-1;
  RETURN (
      SELECT DISTINCT salary
      FROM Employee
      ORDER BY salary DESC
      LIMIT M, 1
  );
END

```

请注意在 SQL 中，查询中子句的执行顺序通常如下：

FROM 子句：指定从中检索数据的表。
WHERE 子句：根据指定的条件筛选行。
GROUP BY 子句：根据指定的列或表达式对行进行分组。
HAVING 子句：根据条件筛选分组的行。
SELECT 子句：选择将在结果集中返回的列或表达式。
ORDER BY 子句：根据指定的列或表达式对结果集进行排序。
LIMIT/OFFSET 子句：限制结果集中返回的行数。
注意：你的 DBMS 可能会以等价但 不同 的顺序执行一个查询。


PostgreSQL
```
CREATE OR REPLACE FUNCTION NthHighestSalary(N INT)
RETURNS TABLE (Salary INT) AS $$
BEGIN
  IF N < 1 THEN
    RETURN QUERY(SELECT NULL::INT AS salary);
  ELSE
    RETURN QUERY (
      SELECT DISTINCT Employee.salary
      FROM Employee
      ORDER BY salary DESC
      LIMIT 1 OFFSET N - 1
    );
  END IF;
END;
$$ LANGUAGE plpgsql;
```

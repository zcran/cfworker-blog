---
title: "leetcode-数据库1"
date: 2026-09-15T10:27:49+08:00
tags: ["leetcode", "数据库"]
draft: false
---


## 组合两个表

表: Person

+-------------+---------+
| 列名         | 类型     |
+-------------+---------+
| PersonId    | int     |
| FirstName   | varchar |
| LastName    | varchar |
+-------------+---------+
personId 是该表的主键（具有唯一值的列）。
该表包含一些人的 ID 和他们的姓和名的信息。


表: Address

+-------------+---------+
| 列名         | 类型    |
+-------------+---------+
| AddressId   | int     |
| PersonId    | int     |
| City        | varchar |
| State       | varchar |
+-------------+---------+
addressId 是该表的主键（具有唯一值的列）。
该表的每一行都包含一个 ID = PersonId 的人的城市和州的信息。


编写解决方案，报告 Person 表中每个人的姓、名、城市和州。如果 personId 的地址不在 Address 表中，则报告为 null 。

以 任意顺序 返回结果表。

MySQL
```
select FirstName, LastName, City, State
from Person left join Address
on Person.PersonId = Address.PersonId
;

```


PostgreSQL
```
SELECT
    p.firstname,
    p.lastname,
    a.city,
    a.state
FROM Person p
LEFT JOIN Address a
    ON p.personid = a.personid;
```

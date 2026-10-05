---
title: "leetcode-贪心166"
date: 2026-10-03T12:55:45+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 采购的最小花费

给你五个整数 cost1, cost2, costBoth, need1 和 need2。

有三种类型的物品可以购买：

· 类型 1 的物品花费 cost1，并仅满足类型 1 的需求 1 个单位。
· 类型 2 的物品花费 cost2，并仅满足类型 2 的需求 1 个单位。
· 类型 3 的物品花费 costBoth，同时满足类型 1 和类型 2 的需求各 1 个单位。

你需要购买足够的物品，使得：

· 满足类型 1 的总需求 至少 为 need1。
· 满足类型 2 的总需求 至少 为 need2。

返回满足这些需求的 最小 可能总花费。




```
impl Solution {
    pub fn minimum_cost(cost1: i32, cost2: i32, cost_both: i32, need1: i32, need2: i32) -> i64 {
        // 三种策略：
        // 1. 分别购买类型 1、类型 2 物品满足各自需求
        // 2. 全部用类型 3（同时满足两者），数量取较大的需求
        // 3. 用类型 3 覆盖较小需求，剩余需求用便宜的那类单独购买
        let (c1, c2, cb) = (cost1 as i64, cost2 as i64, cost_both as i64);
        let (n1, n2) = (need1 as i64, need2 as i64);

        // 策略 1：各买各的
        let plan1 = c1 * n1 + c2 * n2;

        // 让 need1 <= need2，便于统一处理
        let (lo, hi, c_lo) = if n1 <= n2 {
            (n1, n2, c1) // 较小需求对应类型 1
        } else {
            (n2, n1, c2) // 较小需求对应类型 2
        };
        // 补足较大需求时，用另一类型（即与较小需求不同类型的那个）
        let c_hi = if n1 <= n2 { c2 } else { c1 };

        // 策略 2：全用类型 3
        let plan2 = cb * hi;

        // 策略 3：类型 3 覆盖较小需求，剩余部分用单独类型补足
        let plan3 = cb * lo + c_hi * (hi - lo);

        // 注：c_lo 在最优解中只用于策略 1，此处保留以防漏解
        let _ = c_lo;

        plan1.min(plan2).min(plan3)
    }
}
```

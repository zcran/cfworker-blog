---
title: "leetcode-组合数学21"
date: 2026-05-24T09:54:13+08:00
tags: ["leetcode", "组合数学"]
draft: false
---


## 给小朋友们分糖果 I


给你两个正整数 n 和 limit 。

请你将 n 颗糖果分给 3 位小朋友，确保没有任何小朋友得到超过 limit 颗糖果，请你返回满足此条件下的 总方案数 。


```
impl Solution {
    /// 计算将 n 个糖果全部分给 3 个孩子，每个孩子最多分到 limit 个糖果的方案数。
    /// 使用容斥原理，将问题转化为非负整数解个数减去违反上限的情况。
    pub fn distribute_candies(n: i32, limit: i32) -> i32 {
        // 如果糖果总数超过三个孩子的最大容量之和，则不可能分配
        if n > 3 * limit {
            return 0;
        }

        // 辅助函数：计算组合数 C(x, 2) = x*(x-1)/2，当 x < 2 时返回 0
        let comb2 = |x: i64| -> i64 {
            if x < 2 {
                0
            } else {
                x * (x - 1) / 2
            }
        };

        let n = n as i64;
        let limit = limit as i64;
        let m = limit + 1; // 超出限制的阈值（至少拿 limit+1 个）

        // 无上限时的非负整数解个数：C(n+2, 2)
        let total = comb2(n + 2);

        // 至少一个孩子超出上限：令该孩子先取 m 个，剩余 n-m 个自由分配
        let over1 = comb2(n - m + 2);
        // 至少两个孩子超出上限：令这两个孩子各取 m 个，剩余 n-2m 个自由分配
        let over2 = comb2(n - 2 * m + 2);
        // 至少三个孩子超出上限：每人取 m 个，剩余 n-3m 个自由分配
        let over3 = comb2(n - 3 * m + 2);

        // 容斥原理：总方案数 - 违反1个 + 违反2个 - 违反3个
        let ans = total - 3 * over1 + 3 * over2 - over3;
        ans as i32
    }
}
```

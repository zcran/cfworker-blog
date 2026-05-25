---
title: "leetcode-记忆化1"
date: 2026-05-06T20:22:33+08:00
tags: ["leetcode", "记忆化"]
draft: false
---


## 爬楼梯

假设你正在爬楼梯。需要 n 阶你才能到达楼顶。

每次你可以爬 1 或 2 个台阶。你有多少种不同的方法可以爬到楼顶呢？

```
impl Solution {
    /// 爬楼梯问题（斐波那契数列）
    /// 时间复杂度 O(n)，空间 O(1)
    pub fn climb_stairs(n: i32) -> i32 {
        match n {
            1 | 2 => n,                     // 基础情形
            _ => (2..n as usize)            // 计算第 n 项需要迭代 n-2 次
                .fold((1, 2), |(a, b), _| (b, a + b)) // (fib_{k-1}, fib_k) -> (fib_k, fib_{k+1})
                .1,                         // 取出 fib_n
        }
    }
}
```

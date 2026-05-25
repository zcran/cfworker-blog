---
title: "leetcode-记忆化39"
date: 2026-05-06T20:22:34+08:00
tags: ["leetcode", "记忆化"]
draft: false
---

## 三步问题

三步问题。有个小孩正在上楼梯，楼梯有 n 阶台阶，小孩一次可以上 1 阶、2 阶或 3 阶。实现一种方法，计算小孩有多少种上楼梯的方式。结果可能很大，你需要对结果模 1000000007。

```
impl Solution {
    /// 计算爬上 n 阶楼梯的方法数，每次可以走 1、2 或 3 步。
    /// 结果对 1_000_000_007 取模。
    ///
    /// 使用迭代 + 状态压缩（仅保留最近三个值），时间复杂度 O(n)，空间复杂度 O(1)。
    pub fn ways_to_step(n: i32) -> i32 {
        const MOD: i64 = 1_000_000_007;
        let n = n as usize;

        // 边界条件直接返回
        match n {
            0 => return 1,
            1 => return 1,
            2 => return 2,
            _ => {}
        }

        // 使用 i64 避免中间计算溢出
        let (mut a, mut b, mut c) = (1i64, 1i64, 2i64);

        // 迭代计算 dp[i] = dp[i-3] + dp[i-2] + dp[i-1]
        for _ in 3..=n {
            let next = (a + b + c) % MOD;
            a = b;
            b = c;
            c = next;
        }

        c as i32
    }
}
```

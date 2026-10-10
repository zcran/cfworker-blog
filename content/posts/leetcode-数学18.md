---
title: "leetcode-数学18"
date: 2026-10-07T20:39:29+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 质数排列

请你帮忙给从 1 到 n 的数设计排列方案，使得所有的「质数」都应该被放在「质数索引」（索引从 1 开始）上；你需要返回可能的方案总数。

让我们一起来回顾一下「质数」：质数一定是大于 1 的，并且不能用两个小于它的正整数的乘积来表示。

由于答案可能会很大，所以请你返回答案 模 mod 10^9 + 7 之后的结果即可。


```
impl Solution {
    /// 计算 1..=n 的排列方案数，使得所有质数都放在质数索引上。
    ///
    /// 设 1..=n 中质数个数为 p，则非质数个数为 n - p。
    /// 质数索引（1-based）也恰有 p 个，因此：
    ///   - p 个质数可任意排列在 p 个质数索引上，方案数 p!；
    ///   - n - p 个非质数可任意排列在剩余的 n - p 个位置上，方案数 (n - p)!。
    /// 总方案数为 p! × (n - p)!，对大数取模 1e9+7。
    pub fn num_prime_arrangements(n: i32) -> i32 {
        const MOD: i64 = 1_000_000_007;

        // 统计 1..=n 中质数的个数
        let prime_count = (2..=n).filter(|&x| Self::is_prime(x)).count() as i64;
        let non_prime_count = n as i64 - prime_count;

        // 计算 p! * (n-p)! mod MOD
        let mut ans: i64 = 1;
        for i in 1..=prime_count.max(non_prime_count) {
            if i <= prime_count {
                ans = ans * i % MOD;
            }
            if i <= non_prime_count {
                ans = ans * i % MOD;
            }
        }
        ans as i32
    }

    /// 判断 num 是否为质数（试除法，只需试到 √num）。
    #[inline]
    fn is_prime(num: i32) -> bool {
        if num < 2 {
            return false;
        }
        let mut i = 2;
        while i * i <= num {
            if num % i == 0 {
                return false;
            }
            i += 1;
        }
        true
    }
}
```

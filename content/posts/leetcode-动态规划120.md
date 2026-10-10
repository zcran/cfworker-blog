---
title: "leetcode-动态规划120"
date: 2026-10-05T20:40:02+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 将一个数字表示成幂的和的方案数

给你两个 正 整数 n 和 x 。

请你返回将 n 表示成一些 互不相同 正整数的 x 次幂之和的方案数。换句话说，你需要返回互不相同整数 [n1, n2, ..., nk] 的集合数目，满足 n = n1^x + n2^x + ... + nk^x 。

由于答案可能非常大，请你将它对 10^9 + 7 取余后返回。

比方说，n = 160 且 x = 3 ，一个表示 n 的方法是 n = 2^3 + 3^3 + 5^3 。


```
const MOD: i64 = 1_000_000_007;

impl Solution {
    pub fn number_of_ways(n: i32, x: i32) -> i32 {
        let n = n as usize;
        // dp[j]: 用已处理的幂次凑出和 j 的方案数（0-1 背包滚动数组）
        let mut dp = vec![0i64; n + 1];
        dp[0] = 1;

        // 枚举底数 i，其 x 次幂作为一件物品
        let mut i = 1usize;
        loop {
            // 计算 i^x，超过 n 即可停止
            let mut val = 1usize;
            for _ in 0..x {
                val *= i;
                if val > n {
                    break;
                }
            }
            if val > n {
                break;
            }

            // 0-1 背包：每个幂只能使用一次，从大到小更新
            for j in (val..=n).rev() {
                dp[j] = (dp[j] + dp[j - val]) % MOD;
            }
            i += 1;
        }

        dp[n] as i32
    }
}
```

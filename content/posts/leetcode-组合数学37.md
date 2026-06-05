---
title: "leetcode-组合数学37"
date: 2026-05-24T09:54:14+08:00
tags: ["leetcode", "组合数学"]
draft: false
---


## 所有安放棋子方案的曼哈顿距离

给你三个整数 m ，n 和 k 。

给你一个大小为 m x n 的矩形格子，它包含 k 个没有差别的棋子。请你返回所有放置棋子的 合法方案 中，每对棋子之间的曼哈顿距离之和。

一个 合法方案 指的是将所有 k 个棋子都放在格子中且一个格子里 至多 只有一个棋子。

由于答案可能很大， 请你将它对 10^9 + 7 取余 后返回。

两个格子 (xi, yi) 和 (xj, yj) 的曼哈顿距离定义为 |xi - xj| + |yi - yj| 。


```
const MOD: i64 = 1_000_000_007;

impl Solution {
    /// 快速幂取模
    fn mod_exp(mut base: i64, mut exp: i64) -> i64 {
        let mut result = 1;
        base %= MOD;
        while exp > 0 {
            if exp & 1 == 1 {
                result = result * base % MOD;
            }
            base = base * base % MOD;
            exp >>= 1;
        }
        result
    }

    pub fn distance_sum(m: i32, n: i32, k: i32) -> i32 {
        let m = m as i64;
        let n = n as i64;
        let k = k as i64;
        let total = m * n;

        // 没有至少两个棋子，或棋子数超过格子总数时，不存在合法方案
        if k < 2 || k > total {
            return 0;
        }

        // ---------- 所有格子对的曼哈顿距离之和 ----------
        // 行坐标差总和: n^2 * (m^3 - m) / 6
        // 列坐标差总和: m^2 * (n^3 - n) / 6
        // 化简为: m * n * (n * (m^2 - 1) + m * (n^2 - 1)) / 6
        let sum_dist = {
            let part = n * (m * m - 1) + m * (n * n - 1);
            // 使用 i128 避免中间结果溢出 i64
            let total_dist = m as i128 * n as i128 * part as i128 / 6;
            (total_dist % (MOD as i128)) as i64
        };

        // ---------- 组合数 C(total - 2, k - 2) ----------
        // 每对特定的格子同时出现在方案中的方案数
        let n_comb = total - 2;
        let k_comb = k - 2;

        // 动态计算阶乘和阶乘的逆元，仅需要 [0..n_comb] 范围，节约内存且避免全局状态
        let mut fact = vec![1i64; n_comb as usize + 1];
        for i in 1..=n_comb as usize {
            fact[i] = fact[i - 1] * i as i64 % MOD;
        }

        let mut inv_fact = vec![1i64; n_comb as usize + 1];
        inv_fact[n_comb as usize] = Self::mod_exp(fact[n_comb as usize], MOD - 2);
        for i in (1..=n_comb as usize).rev() {
            inv_fact[i - 1] = inv_fact[i] * i as i64 % MOD;
        }

        let comb = fact[n_comb as usize] * inv_fact[k_comb as usize] % MOD
            * inv_fact[(n_comb - k_comb) as usize] % MOD;

        (sum_dist * comb % MOD) as i32
    }
}
```

---
title: "leetcode-组合数学49"
date: 2026-05-24T09:54:14+08:00
tags: ["leetcode", "组合数学"]
draft: false
---


## 古董键盘

小扣在秋日市集购买了一个古董键盘。由于古董键盘年久失修，键盘上只有 26 个字母 a~z 可以按下，且每个字母最多仅能被按 k 次。

小扣随机按了 n 次按键，请返回小扣总共有可能按出多少种内容。由于数字较大，最终答案需要对 1000000007 (1e9 + 7) 取模。


```
impl Solution {
    /// 计算按 n 次按键（每个字母最多按 k 次）可能产生的不同字符串总数，对 1e9+7 取模。
    pub fn keyboard(k: i32, n: i32) -> i32 {
        const MOD: i64 = 1_000_000_007;
        let k = k as usize;
        let n = n as usize;

        // ---------- 预计算阶乘和逆元，用于 O(1) 获取组合数 ----------
        let mut fact = vec![1i64; n + 1];
        for i in 1..=n {
            fact[i] = fact[i - 1] * i as i64 % MOD;
        }
        let mut inv_fact = vec![1i64; n + 1];
        inv_fact[n] = pow_mod(fact[n], MOD - 2, MOD);
        for i in (1..=n).rev() {
            inv_fact[i - 1] = inv_fact[i] * i as i64 % MOD;
        }
        // 组合数函数 C(i, x) mod MOD
        let comb = |i: usize, x: usize| -> i64 {
            if x > i {
                return 0;
            }
            fact[i] * inv_fact[x] % MOD * inv_fact[i - x] % MOD
        };

        // ---------- 滚动数组 DP ----------
        // prev[i] 表示使用前 j-1 种字母时，按 i 次的方案数
        let mut prev = vec![0i64; n + 1];
        prev[0] = 1; // 使用 0 种字母，只有按 0 次一种方案

        for j in 1..=26 {
            let mut cur = vec![0i64; n + 1];
            cur[0] = 1; // 按 0 次，无论有多少字母，只有空串一种方案
            for i in 1..=n {
                let mut sum = 0i64;
                // 当前字母最多按 k 次，且不能超过总次数 i
                let max_x = k.min(i);
                for x in 0..=max_x {
                    // 从 i 个位置中选 x 个放当前字母，剩余 i-x 个位置由前 j-1 种字母填充
                    let ways = comb(i, x);
                    sum = (sum + prev[i - x] * ways) % MOD;
                }
                cur[i] = sum;
            }
            prev = cur; // 滚动到下一层
        }

        prev[n] as i32
    }
}

/// 快速幂：计算 x^n mod m
fn pow_mod(mut x: i64, mut n: i64, m: i64) -> i64 {
    let mut res = 1;
    while n > 0 {
        if n & 1 == 1 {
            res = res * x % m;
        }
        x = x * x % m;
        n >>= 1;
    }
    res
}
```

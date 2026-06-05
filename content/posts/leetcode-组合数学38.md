---
title: "leetcode-组合数学38"
date: 2026-05-24T09:54:14+08:00
tags: ["leetcode", "组合数学"]
draft: false
---


## 最多 K 个元素的子序列的最值之和

给你一个整数数组 nums 和一个正整数 k，返回所有长度最多为 k 的 子序列 中 最大值 与 最小值 之和的总和。

非空子序列 是指从另一个数组中删除一些或不删除任何元素（且不改变剩余元素的顺序）得到的数组。

由于答案可能非常大，请返回对 10^9 + 7 取余数的结果。



```
const MOD: i64 = 1_000_000_007;

impl Solution {
    /// 快速幂取模
    fn mod_pow(mut x: i64, mut exp: i64) -> i64 {
        let mut res = 1;
        x %= MOD;
        while exp > 0 {
            if exp & 1 == 1 {
                res = res * x % MOD;
            }
            x = x * x % MOD;
            exp >>= 1;
        }
        res
    }

    pub fn min_max_sums(nums: Vec<i32>, k: i32) -> i32 {
        let mut nums = nums;
        nums.sort_unstable();
        let n = nums.len();
        let k = k as usize;
        if n == 0 || k == 0 {
            return 0;
        }

        // ---------- 预计算阶乘与逆阶乘，仅到 n ----------
        let max_n = n;
        let mut fact = vec![1i64; max_n + 1];
        for i in 1..=max_n {
            fact[i] = fact[i - 1] * i as i64 % MOD;
        }
        let mut inv_fact = vec![1i64; max_n + 1];
        inv_fact[max_n] = Self::mod_pow(fact[max_n], MOD - 2);
        for i in (1..=max_n).rev() {
            inv_fact[i - 1] = inv_fact[i] * i as i64 % MOD;
        }

        // 组合数 C(n, m)
        let comb = |n: usize, m: usize| -> i64 {
            if m > n {
                return 0;
            }
            fact[n] * inv_fact[m] % MOD * inv_fact[n - m] % MOD
        };

        let mut ans: i64 = 0;
        // 设 s(i) = Σ_{j=0}^{min(k-1, i)} C(i, j)
        // 初始：i = 0 时，s = 1
        let mut s: i64 = 1;
        ans = (ans + s * (nums[0] as i64 + nums[n - 1] as i64)) % MOD;

        let limit = k - 1; // 除了当前元素外，最多还能选择的元素个数

        // 利用组合恒等式 O(n) 递推 s(i)，避免内部循环
        for i in 1..n {
            if i <= limit {
                // 当 i <= k-1 时，s(i) = 2^i，可由 s(i-1) 直接加倍
                s = s * 2 % MOD;
            } else {
                // 当 i > k-1 时，s(i) = 2 * s(i-1) - C(i-1, k-1)
                s = (s * 2 % MOD - comb(i - 1, limit) + MOD) % MOD;
            }
            // 第 i 小的元素作为最大值的方案数 = s
            // 第 i 小的元素作为最小值的方案数同样 = s（对称性）
            ans = (ans + s * ((nums[i] as i64 + nums[n - 1 - i] as i64) % MOD)) % MOD;
        }

        ans as i32
    }
}
```

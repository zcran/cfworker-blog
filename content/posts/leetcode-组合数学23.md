---
title: "leetcode-组合数学23"
date: 2026-05-24T09:54:13+08:00
tags: ["leetcode", "组合数学"]
draft: false
---


## 统计感冒序列的数目


给你一个整数 n 和一个下标从 0 开始的整数数组 sick ，数组按 升序 排序。

有 n 位小朋友站成一排，按顺序编号为 0 到 n - 1 。数组 sick 包含一开始得了感冒的小朋友的位置。如果位置为 i 的小朋友得了感冒，他会传染给下标为 i - 1 或者 i + 1 的小朋友，前提 是被传染的小朋友存在且还没有得感冒。每一秒中， 至多一位 还没感冒的小朋友会被传染。

经过有限的秒数后，队列中所有小朋友都会感冒。感冒序列 指的是 所有 一开始没有感冒的小朋友最后得感冒的顺序序列。请你返回所有感冒序列的数目。

由于答案可能很大，请你将答案对 10^9 + 7 取余后返回。

注意，感冒序列 不 包含一开始就得了感冒的小朋友的下标。



```
impl Solution {
    pub fn number_of_sequence(n: i32, sick: Vec<i32>) -> i32 {
        const MOD: i64 = 1_000_000_007;
        let n = n as usize;
        let m = sick.len();
        let total = n - m; // 健康小朋友的总数

        // 快速幂取模 (base^exp % MOD)
        fn pow_mod(mut base: i64, mut exp: usize) -> i64 {
            let mut res = 1;
            while exp > 0 {
                if exp & 1 == 1 {
                    res = (res * base) % MOD;
                }
                base = (base * base) % MOD;
                exp >>= 1;
            }
            res
        }

        // 预计算阶乘和逆元，长度至多 total+1
        let mut fac = vec![1i64; total + 1];
        for i in 1..=total {
            fac[i] = fac[i - 1] * i as i64 % MOD;
        }
        let mut inv_fac = vec![1i64; total + 1];
        if total >= 1 {
            inv_fac[total] = pow_mod(fac[total], MOD as usize - 2);
            for i in (1..=total).rev() {
                inv_fac[i - 1] = inv_fac[i] * i as i64 % MOD;
            }
        }

        // 组合数 C(n, k) = fac[n] * inv_fac[k] * inv_fac[n-k] % MOD
        let comb = |n: usize, k: usize| -> i64 {
            if k > n { return 0; }
            fac[n] * inv_fac[k] % MOD * inv_fac[n - k] % MOD
        };

        // 左右两端的长度
        let left_len = sick[0] as usize;          // 0 到第一个病人之间的健康人数
        let right_len = n - sick[m - 1] as usize - 1; // 最后一个病人到 n-1 之间的健康人数
        let mut ans = comb(total, left_len) * comb(total - left_len, right_len) % MOD;
        let mut remaining = total - left_len - right_len; // 剩余未安排的健康人数（中间段）
        let mut e = 0; // 2 的幂次，对应中间段内部顺序的自由度

        // 处理相邻病人之间的连续健康段
        for i in 1..m {
            let gap = (sick[i] - sick[i - 1] - 1) as usize; // 两个病人之间的健康人数
            if gap > 0 {
                e += gap - 1;                 // 内部顺序有 2^(gap-1) 种
                ans = ans * comb(remaining, gap) % MOD;
                remaining -= gap;
            }
        }

        // 乘以所有中间段的内部顺序数
        ans = ans * pow_mod(2, e) % MOD;
        ans as i32
    }
}
```

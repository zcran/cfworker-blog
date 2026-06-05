---
title: "leetcode-组合数学47"
date: 2026-05-24T09:54:14+08:00
tags: ["leetcode", "组合数学"]
draft: false
---


## 给定范围内 K 位数字之和


给你三个整数 l、r 和 k。


考虑所有由 恰好 k 位数字组成的整数里，每一位数字都是从整数范围 [l, r]（闭区间）中独立选择的。如果该范围内包含 0，则允许出现前导零。

返回一个整数，代表 所有此类数字之和。由于答案可能很大，请将其对 10^9 + 7 取模 后返回。


```
impl Solution {
    const MOD: i64 = 1_000_000_007;

    /// 快速幂：计算 x^n mod MOD
    fn pow_mod(mut x: i64, mut n: i64) -> i64 {
        let mut res = 1;
        while n > 0 {
            if n & 1 == 1 {
                res = (res * x) % Self::MOD;
            }
            x = (x * x) % Self::MOD;
            n >>= 1;
        }
        res
    }

    /// 返回区间 [l, r] 内所有数的 "k 次方和" 相关的某个值（具体公式见题解）
    pub fn sum_of_numbers(l: i32, r: i32, k: i32) -> i32 {
        let l = l as i64;
        let r = r as i64;
        let k = k as i64;
        let m = r - l + 1;                 // 区间长度
        let a = (l + r) % Self::MOD * (m % Self::MOD) % Self::MOD;
        let b = (Self::pow_mod(10, k) - 1 + Self::MOD) % Self::MOD;
        let inv_18 = Self::pow_mod(18, Self::MOD - 2);   // 18 的模逆元
        let c = Self::pow_mod(m, k - 1);
        let ans = a * b % Self::MOD * inv_18 % Self::MOD * c % Self::MOD;
        ans as i32
    }
}
```

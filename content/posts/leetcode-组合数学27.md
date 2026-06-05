---
title: "leetcode-组合数学27"
date: 2026-05-24T09:54:13+08:00
tags: ["leetcode", "组合数学"]
draft: false
---


##  秒后第 N 个元素的值


给你两个整数 n 和 k。

最初，你有一个长度为 n 的整数数组 a，对所有 0 <= i <= n - 1，都有 a[i] = 1 。每过一秒，你会同时更新每个元素为其前面所有元素的和加上该元素本身。例如，一秒后，a[0] 保持不变，a[1] 变为 a[0] + a[1]，a[2] 变为 a[0] + a[1] + a[2]，以此类推。

返回 k 秒后 a[n - 1] 的值。

由于答案可能非常大，返回其对 10^9 + 7 取余 后的结果。

```
impl Solution {
    const MOD: i64 = 1_000_000_007;

    /// 返回 k 秒后数组第 n-1 个元素的值（模 1e9+7）。
    /// 数学推导：结果为组合数 C(n + k - 1, k) 或等价形式。
    pub fn value_after_k_seconds(n: i32, k: i32) -> i32 {
        let n = n as i64;
        let k = k as i64;
        // 边界情况：当 n == 0 或 k == 0 时，结果为 1
        if n == 0 || k == 0 {
            return 1;
        }
        let res = Self::comb(n + k - 1, k);
        res as i32
    }

    /// 计算组合数 C(n, k) mod MOD，使用递推乘法与模逆元。
    /// 时间复杂度 O(min(k, n-k) * log MOD)，空间 O(1)。
    fn comb(n: i64, k: i64) -> i64 {
        if k < 0 || k > n {
            return 0;
        }
        // 利用对称性，取较小的 k 以减少循环次数
        let k = if k > n - k { n - k } else { k };
        let mut res = 1i64;
        // C(n, k) = ∏_{i=1}^{k} (n - k + i) / i
        for i in 1..=k {
            let numerator = (n - k + i) % Self::MOD;
            res = res * numerator % Self::MOD;
            res = res * Self::mod_inv(i) % Self::MOD;
        }
        res
    }

    /// 模逆元：利用费马小定理 a^(MOD-2) ≡ a^(-1) (mod MOD)
    fn mod_inv(a: i64) -> i64 {
        Self::pow_mod(a, Self::MOD - 2)
    }

    /// 快速幂取模
    fn pow_mod(mut x: i64, mut exp: i64) -> i64 {
        let mut res = 1;
        while exp > 0 {
            if exp & 1 == 1 {
                res = res * x % Self::MOD;
            }
            x = x * x % Self::MOD;
            exp >>= 1;
        }
        res
    }
}
```

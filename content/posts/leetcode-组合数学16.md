---
title: "leetcode-组合数学16"
date: 2026-05-24T09:54:13+08:00
tags: ["leetcode", "组合数学"]
draft: false
---


## 统计理想数组的数目

给你两个整数 n 和 maxValue ，用于描述一个 理想数组 。

对于下标从 0 开始、长度为 n 的整数数组 arr ，如果满足以下条件，则认为该数组是一个 理想数组 ：

每个 arr[i] 都是从 1 到 maxValue 范围内的一个值，其中 0 <= i < n 。
每个 arr[i] 都可以被 arr[i - 1] 整除，其中 0 < i < n 。

返回长度为 n 的 不同 理想数组的数目。由于答案可能很大，返回对 10^9 + 7 取余的结果。


```
impl Solution {
    const MOD: i64 = 1_000_000_007;

    /// 计算理想数组的个数
    /// 算法：每个数 x 可分解为质因子指数 (p1, p2, ...)，
    ///       长度为 n 的数组对应每个质因子的指数序列是非递减的，
    ///       方案数为 ∏ C(n + pi - 1, pi)。
    ///       对所有 x = 1..=max_value 求和。
    pub fn ideal_arrays(n: i32, max_val: i32) -> i32 {
        let n = n as i64;
        let max_val = max_val as usize;

        // 质因子指数的上界（2^20 > 1e6，足够覆盖所有 max_val ≤ 1e5）
        const MAX_P: usize = 20;

        // 预计算 1..=MAX_P 的乘法逆元（模 MOD）
        let mut inv = [0i64; MAX_P + 1];
        inv[1] = 1;
        for i in 2..=MAX_P {
            inv[i] = Self::MOD - (Self::MOD / i as i64) * inv[Self::MOD as usize % i] % Self::MOD;
        }

        // 线性筛求最小质因子
        let mut spf = vec![0; max_val + 1];
        let mut primes = Vec::new();
        for i in 2..=max_val {
            if spf[i] == 0 {
                spf[i] = i;
                primes.push(i);
            }
            for &p in &primes {
                if p > spf[i] || i * p > max_val {
                    break;
                }
                spf[i * p] = p;
            }
        }

        let mut ans = 0i64;

        // 遍历每个可能的数字 x
        for mut x in 1..=max_val {
            let mut mul = 1i64;
            // 分解质因数，得到每个质因子的指数
            while x > 1 {
                let p = spf[x];
                let mut cnt = 0;
                while x % p == 0 {
                    x /= p;
                    cnt += 1;
                }
                // 计算 C(n + cnt - 1, cnt) 并乘入 mul
                let mut comb = 1i64;
                for i in 1..=cnt {
                    // (n + i - 1) / i  逐步计算，避免溢出
                    let numerator = (n + i as i64 - 1) % Self::MOD;
                    comb = comb * numerator % Self::MOD;
                    comb = comb * inv[i as usize] % Self::MOD;
                }
                mul = mul * comb % Self::MOD;
            }
            ans = (ans + mul) % Self::MOD;
        }

        ans as i32
    }
}
```

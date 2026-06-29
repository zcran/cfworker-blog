---
title: "leetcode-分治12"
date: 2026-06-13T10:51:38+08:00
tags: ["leetcode", "分治"]
draft: false
---


## 超级次方

你的任务是计算 a^b 对 1337 取模，a 是一个正整数，b 是一个非常大的正整数且会以数组形式给出。


```
impl Solution {
    pub fn super_pow(a: i32, b: Vec<i32>) -> i32 {
        const M: i64 = 1337;
        let mut pow = |mut x: i64, mut n: i64| {
            let mut r = 1;
            while n > 0 {
                if n & 1 == 1 { r = r * x % M; }
                x = x * x % M;
                n >>= 1;
            }
            r
        };
        let a = a as i64 % M;
        b.iter().fold(1, |acc, &d| pow(acc, 10) * pow(a, d as i64) % M) as i32
    }
}
```


```
impl Solution {
    /// 计算 a^b % 1337，其中 b 以十进制数组形式给出
    /// 例如：a = 2, b = [1, 0] 表示 2^10 % 1337
    pub fn super_pow(a: i32, b: Vec<i32>) -> i32 {
        const MOD: i64 = 1337;

        // 快速幂：计算 (x^n) % MOD
        fn pow_mod(mut x: i64, mut n: i64) -> i64 {
            let mut result = 1;
            while n > 0 {
                if n & 1 == 1 {
                    result = (result * x) % MOD;
                }
                x = (x * x) % MOD;
                n >>= 1;
            }
            result
        }

        let a = (a % MOD as i32) as i64; // 取模缩小 a 的范围，避免溢出

        // 核心递推公式：
        // 假设当前结果是 ans，新来一位数字 d
        // 则新结果 = (ans^10) * (a^d) % MOD
        // 原理：原指数整体左移一位（十进制）并加上新低位
        let mut result = 1;
        for &digit in &b {
            result = pow_mod(result, 10) * pow_mod(a, digit as i64) % MOD;
        }

        result as i32
    }
}
```

---
title: "leetcode-动态规划18"
date: 2026-10-05T20:39:55+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 超级丑数

超级丑数 是一个正整数，并满足其所有质因数都出现在质数数组 primes 中。

给你一个整数 n 和一个整数数组 primes ，返回第 n 个 超级丑数 。

题目数据保证第 n 个 超级丑数 在 32-bit 带符号整数范围内。




```
impl Solution {
    /// 动态规划 + 多指针
    pub fn nth_super_ugly_number(n: i32, primes: Vec<i32>) -> i32 {
        let k = primes.len();
        let n = n as usize;

        // dp[i]：第 i+1 个超级丑数
        let mut dp = vec![1i64; n];
        // index[j]：第 j 个质数当前对应的 dp 下标（即"指针"）
        let mut index = vec![0usize; k];

        for i in 1..n {
            // 用 i64 计算，避免 primes[j] * dp[index[j]] 溢出 i32
            let mut next = i64::MAX;
            for j in 0..k {
                next = next.min(primes[j] as i64 * dp[index[j]]);
            }
            dp[i] = next;

            // 所有能产生该值的指针都前进一位，避免重复
            for j in 0..k {
                if primes[j] as i64 * dp[index[j]] == next {
                    index[j] += 1;
                }
            }
        }

        dp[n - 1] as i32
    }
}
```

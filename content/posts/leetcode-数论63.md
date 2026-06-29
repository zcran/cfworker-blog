---
title: "leetcode-数论63"
date: 2026-06-20T11:09:57+08:00
tags: ["leetcode", "数论"]
draft: false
---


## 可表示为连续质数和的最大质数

给你一个整数 n。

返回小于或等于 n 的最大质数，该质数可以表示为从 2 开始的一个或多个 连续质数 之和。如果不存在这样的质数，则返回 0。

质数是大于 1 的自然数，且只有两个因数：1 和它本身。


```
const MX: usize = 500_000;

lazy_static::lazy_static! {
    /// 所有可表示为从2开始的连续质数之和的质数（特殊质数），已排序
    static ref SPECIAL_PRIMES: Vec<i32> = {
        // 埃氏筛法：标记质数
        let mut is_prime = vec![true; MX + 1];
        is_prime[0] = false;
        is_prime[1] = false;

        let mut primes = Vec::new();
        for i in 2..=MX {
            if is_prime[i] {
                primes.push(i as i32);
                // 从 i² 开始标记倍数
                let mut j = i * i;
                while j <= MX {
                    is_prime[j] = false;
                    j += i;
                }
            }
        }

        // 计算连续质数前缀和，筛选出质数
        let mut special = Vec::new();
        let mut sum = 0i32;
        for &p in &primes {
            sum += p;
            if sum > MX as i32 { break; }
            if is_prime[sum as usize] {
                special.push(sum);
            }
        }
        special
    };
}

impl Solution {
    /// 返回 ≤ n 的最大特殊质数（从2开始的连续质数之和）
    /// 如果不存在则返回 0
    pub fn largest_prime(n: i32) -> i32 {
        if n < 2 { return 0; }

        // 二分查找：找到第一个 > n 的位置，返回前一个
        match SPECIAL_PRIMES.binary_search(&n) {
            Ok(pos) => SPECIAL_PRIMES[pos],      // 恰好等于 n
            Err(pos) => {
                if pos == 0 { 0 }                // 没有 ≤ n 的特殊质数
                else { SPECIAL_PRIMES[pos - 1] } // 返回小于 n 的最大值
            }
        }
    }
}
```

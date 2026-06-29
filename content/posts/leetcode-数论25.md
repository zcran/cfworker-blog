---
title: "leetcode-数论25"
date: 2026-06-20T11:09:55+08:00
tags: ["leetcode", "数论"]
draft: false
---


## 使用质因数之和替换后可以取到的最小值

给你一个正整数 n 。

请你将 n 的值替换为 n 的 质因数 之和，重复这一过程。

注意，如果 n 能够被某个质因数多次整除，则在求和时，应当包含这个质因数同样次数。

返回 n 可以取到的最小值。


```
impl Solution {
    /// 将 n 替换为其质因数之和，直到值不再变化
    ///
    /// 算法原理：重复计算 n 的所有质因数之和，直到结果不再变化
    pub fn smallest_value(mut n: i32) -> i32 {
        loop {
            let sum = sum_of_prime_factors(n);
            if sum == n {
                return n;
            }
            n = sum;
        }
    }
}

/// 计算 n 的所有质因数之和（包括重复的质因数）
fn sum_of_prime_factors(mut n: i32) -> i32 {
    let mut sum = 0;
    let mut factor = 2;

    // 处理因子 2 到 sqrt(n)
    while factor * factor <= n {
        while n % factor == 0 {
            sum += factor;
            n /= factor;
        }
        factor += 1;
    }

    // 如果 n > 1，说明剩下一个质因数
    if n > 1 {
        sum += n;
    }

    sum
}
```

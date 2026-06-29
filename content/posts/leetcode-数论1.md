---
title: "leetcode-数论1"
date: 2026-06-20T11:09:54+08:00
tags: ["leetcode", "数论"]
draft: false
---


## 计数质数

给定整数 n ，返回 所有小于非负整数 n 的质数的数量 。


```
impl Solution {
    pub fn count_primes(n: i32) -> i32 {
        // 小于2没有质数
        if n < 3 {
            return 0;
        }

        let n = n as usize;
        // 使用字节数组节省内存（false 表示合数，true 表示质数）
        let mut is_prime = vec![true; n];
        is_prime[0] = false;
        is_prime[1] = false;

        let limit = (n as f64).sqrt() as usize;
        let mut count = 0;

        for i in 2..n {
            if is_prime[i] {
                count += 1;
                // 只对 <= sqrt(n) 的质数进行筛除
                if i <= limit {
                    let mut j = i * i;
                    while j < n {
                        is_prime[j] = false;
                        j += i;
                    }
                }
            }
        }

        count
    }
}
```

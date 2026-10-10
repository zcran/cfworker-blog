---
title: "leetcode-数学28"
date: 2026-10-07T20:39:30+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 四因数

给你一个整数数组 nums，请你返回该数组中恰有四个因数的这些整数的各因数之和。如果数组中不存在满足题意的整数，则返回 0 。


```
impl Solution {
    /// 返回数组中「恰有四个因数」的整数，其四个因数之和的总和。
    ///
    /// 恰有四个因数的整数只有两种形式：
    ///   1. p³（p 为质数）：因数为 1, p, p², p³；
    ///   2. p × q（p、q 为不同质数）：因数为 1, p, q, pq。
    /// 因此先筛出上限内的质数，预处理出所有符合条件的数与对应的因数和，
    /// 再对数组求和即可，避免对每个数单独分解质因数。
    pub fn sum_four_divisors(nums: Vec<i32>) -> i32 {
        // 数组元素上限
        const C: usize = 100_000;
        // C 的立方根，用于筛选 p³ 形式
        const C3: usize = 46;

        // 埃拉托斯特尼筛法
        let mut is_prime = vec![true; C + 1];
        is_prime[0] = false;
        is_prime[1] = false;
        let mut primes = Vec::new();
        for i in 2..=C {
            if is_prime[i] {
                primes.push(i);
                // 从 i*i 开始标记，避免重复
                let mut j = i * i;
                while j <= C {
                    is_prime[j] = false;
                    j += i;
                }
            }
        }

        // 用 HashMap 记录每个「四因数」数对应的因数和
        let mut factor_sum = std::collections::HashMap::new();

        // 形式一：p³
        for &p in &primes {
            if p > C3 {
                break;
            }
            let key = p * p * p;
            factor_sum.insert(key, (1 + p + p * p + p * p * p) as i32);
        }

        // 形式二：p × q（p < q）
        for i in 0..primes.len() {
            let p = primes[i];
            for j in (i + 1)..primes.len() {
                let q = primes[j];
                let key = p * q;
                if key > C {
                    break;
                }
                factor_sum.insert(key, (1 + p + q + key) as i32);
            }
        }

        nums.iter().filter_map(|n| factor_sum.get(&(*n as usize))).sum()
    }
}
```

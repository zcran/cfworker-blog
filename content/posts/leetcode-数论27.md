---
title: "leetcode-数论27"
date: 2026-06-20T11:09:55+08:00
tags: ["leetcode", "数论"]
draft: false
---


## 数组乘积中的不同质因数数目

给你一个正整数数组 nums ，对 nums 所有元素求积之后，找出并返回乘积中 不同质因数 的数目。

注意：

质数 是指大于 1 且仅能被 1 及自身整除的数字。

如果 val2 / val1 是一个整数，则整数 val1 是另一个整数 val2 的一个因数。


```
use std::collections::HashSet;

impl Solution {
    /// 计算数组所有元素乘积的不同质因数个数
    ///
    /// # 示例
    /// ```
    /// use solution::Solution;
    /// assert_eq!(Solution::distinct_prime_factors(vec![2, 3, 4]), 2);
    /// assert_eq!(Solution::distinct_prime_factors(vec![1, 2, 3, 4]), 2);
    /// ```
    pub fn distinct_prime_factors(nums: Vec<i32>) -> i32 {
        let mut primes = HashSet::new();

        for mut num in nums {
            // 处理质因数 2
            if num % 2 == 0 {
                primes.insert(2);
                while num % 2 == 0 {
                    num /= 2;
                }
            }

            // 处理奇数质因数（从 3 开始，步长为 2）
            let mut factor = 3;
            while factor * factor <= num {
                if num % factor == 0 {
                    primes.insert(factor);
                    while num % factor == 0 {
                        num /= factor;
                    }
                }
                factor += 2;
            }

            // 如果 num > 1，说明剩下一个质因数
            if num > 1 {
                primes.insert(num);
            }
        }

        primes.len() as i32
    }
}
```

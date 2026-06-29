---
title: "leetcode-数论56"
date: 2026-06-20T11:09:57+08:00
tags: ["leetcode", "数论"]
draft: false
---


## 检查元素频次是否为质数

给你一个整数数组 nums。

如果数组中任一元素的 频次 是 质数，返回 true；否则，返回 false。

元素 x 的 频次 是它在数组中出现的次数。

质数是一个大于 1 的自然数，并且只有两个因数：1 和它本身。




```
impl Solution {
    pub fn check_prime_frequency(nums: Vec<i32>) -> bool {
        // 统计每个元素出现的次数
        use std::collections::HashMap;
        let mut freq = HashMap::new();
        for &num in &nums {
            *freq.entry(num).or_insert(0) += 1;
        }

        // 检查是否有频次是质数
        freq.values().any(|&count| Self::is_prime(count))
    }

    /// 判断一个数是否为质数（适用于 count <= 100）
    #[inline]
    fn is_prime(n: i32) -> bool {
        if n < 2 {
            return false;
        }
        if n == 2 || n == 3 {
            return true;
        }
        if n % 2 == 0 || n % 3 == 0 {
            return false;
        }

        // 使用 6k ± 1 优化
        let limit = (n as f64).sqrt() as i32;
        let mut i = 5;
        while i <= limit {
            if n % i == 0 || n % (i + 2) == 0 {
                return false;
            }
            i += 6;
        }
        true
    }
}
```

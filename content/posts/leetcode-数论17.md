---
title: "leetcode-数论17"
date: 2026-06-20T11:09:55+08:00
tags: ["leetcode", "数论"]
draft: false
---


## 统计可以被 K 整除的下标对数目

给你一个下标从 0 开始、长度为 n 的整数数组 nums 和一个整数 k ，返回满足下述条件的下标对 (i, j) 的数目：

0 <= i < j <= n - 1 且

nums[i] * nums[j] 能被 k 整除。


```
use std::collections::HashMap;

impl Solution {
    pub fn count_pairs(nums: Vec<i32>, k: i32) -> i64 {
        // 统计每个数与k的最大公约数出现的次数
        let mut freq: HashMap<i32, i64> = HashMap::new();
        for &num in &nums {
            *freq.entry(gcd(num, k)).or_insert(0) += 1;
        }

        let mut ans = 0i64;
        let keys: Vec<i32> = freq.keys().cloned().collect();

        // 遍历所有可能的gcd组合
        for i in 0..keys.len() {
            let x = keys[i];
            let occx = freq[&x];
            for j in 0..keys.len() {
                let y = keys[j];
                let occy = freq[&y];
                // 如果x*y能被k整除，则所有组合都有效
                if (x as i64 * y as i64) % k as i64 == 0 {
                    ans += occx * occy;
                }
            }
        }

        // 减去自配对的情况（i == j）
        for &num in &nums {
            if (num as i64 * num as i64) % k as i64 == 0 {
                ans -= 1;
            }
        }

        // 每对计算了两次，除以2
        ans / 2
    }
}

/// 计算两个数的最大公约数
fn gcd(mut a: i32, mut b: i32) -> i32 {
    while b != 0 {
        let temp = b;
        b = a % b;
        a = temp;
    }
    a
}
```

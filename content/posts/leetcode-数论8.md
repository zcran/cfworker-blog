---
title: "leetcode-数论8"
date: 2026-06-20T11:09:55+08:00
tags: ["leetcode", "数论"]
draft: false
---


## n 的第 k 个因子

给你两个正整数 n 和 k 。

如果正整数 i 满足 n % i == 0 ，那么我们就说正整数 i 是整数 n 的因子。

考虑整数 n 的所有因子，将它们 升序排列 。请你返回第 k 个因子。如果 n 的因子数少于 k ，请你返回 -1 。


```
impl Solution {
    /// 返回 n 的所有因子按升序排列后的第 k 个因子
    ///
    /// 核心思路：枚举因子直到找到第 k 个
    /// 优化：只需要遍历到 sqrt(n)，利用成对出现的因子特性
    pub fn kth_factor(n: i32, k: i32) -> i32 {
        let n = n as usize;
        let k = k as usize;
        let mut count = 0;

        // ---------- 1. 遍历前半部分因子（1..sqrt(n)） ----------
        // 寻找第 k 个因子，如果在前半部分找到则直接返回
        for i in 1..=((n as f64).sqrt() as usize) {
            if n % i == 0 {
                count += 1;
                if count == k {
                    return i as i32;
                }
            }
        }

        // ---------- 2. 遍历后半部分因子（sqrt(n)..n） ----------
        // 成对出现的因子：如果 i 是因子，则 n/i 也是因子
        // 从 sqrt(n) 向下遍历，找到对应的配对因子
        for i in (1..=((n as f64).sqrt() as usize)).rev() {
            if n % i == 0 {
                let paired = n / i;
                // 避免重复计数（当 i 的平方等于 n 时）
                if paired != i {
                    count += 1;
                    if count == k {
                        return paired as i32;
                    }
                }
            }
        }

        -1
    }
}
```

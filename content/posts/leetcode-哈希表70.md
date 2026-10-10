---
title: "leetcode-哈希表70"
date: 2026-10-09T20:08:00+08:00
tags: ["leetcode", "哈希表"]
draft: false
---


## 优质数对的总数 I

给你两个整数数组 nums1 和 nums2，长度分别为 n 和 m。同时给你一个正整数 k。

如果 nums1[i] 可以除尽 nums2[j] * k，则称数对 (i, j) 为 优质数对（0 <= i <= n - 1, 0 <= j <= m - 1）。

返回 优质数对 的总数。


```
use std::collections::HashMap;

impl Solution {
    pub fn number_of_pairs(nums1: Vec<i32>, nums2: Vec<i32>, k: i32) -> i32 {
        // 统计 nums2 中每个值出现的次数（同一 b 对结果的贡献相同）
        let mut cnt: HashMap<i32, i32> = HashMap::new();
        for &b in &nums2 {
            *cnt.entry(b).or_insert(0) += 1;
        }

        let mut ans = 0;
        // 对每个 a，只需遍历 nums2 中出现过的不同 b 值
        for &a in &nums1 {
            for (&b, &c) in &cnt {
                if a % (b * k) == 0 {
                    ans += c;
                }
            }
        }

        ans
    }
}
```

---
title: "leetcode-哈希表71"
date: 2026-10-09T20:08:00+08:00
tags: ["leetcode", "哈希表"]
draft: false
---


## 优质数对的总数 II

给你两个整数数组 nums1 和 nums2，长度分别为 n 和 m。同时给你一个正整数 k。

如果 nums1[i] 可以被 nums2[j] * k 整除，则称数对 (i, j) 为 优质数对（0 <= i <= n - 1, 0 <= j <= m - 1）。

返回 优质数对 的总数。


```
use std::collections::HashMap;

impl Solution {
    pub fn number_of_pairs(nums1: Vec<i32>, nums2: Vec<i32>, k: i32) -> i64 {
        // 统计 nums1 中每个值的出现次数，并记录其最大值
        let mut cnt1: HashMap<i32, i64> = HashMap::new();
        let mut max1 = 0;
        for &v in &nums1 {
            *cnt1.entry(v).or_insert(0) += 1;
            max1 = max1.max(v);
        }

        // 统计 nums2 中每个值的出现次数
        let mut cnt2: HashMap<i32, i64> = HashMap::new();
        for &v in &nums2 {
            *cnt2.entry(v).or_insert(0) += 1;
        }

        // 对每个不同的 b，枚举其在 nums1 范围内的倍数 b*k, 2*b*k, ...
        let mut ans = 0i64;
        for (&b, &cb) in &cnt2 {
            let step = b as i64 * k as i64;
            if step <= 0 {
                continue; // 防止非法步长
            }
            let mut m = step;
            while m <= max1 as i64 {
                if let Some(&ca) = cnt1.get(&(m as i32)) {
                    ans += ca * cb;
                }
                m += step;
            }
        }

        ans
    }
}
```

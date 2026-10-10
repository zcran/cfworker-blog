---
title: "leetcode-哈希表10"
date: 2026-10-09T20:07:55+08:00
tags: ["leetcode", "哈希表"]
draft: false
---


## 四数相加 II

给你四个整数数组 nums1、nums2、nums3 和 nums4 ，数组长度都是 n ，请你计算有多少个元组 (i, j, k, l) 能满足：

0 <= i, j, k, l < n

nums1[i] + nums2[j] + nums3[k] + nums4[l] == 0


```
use std::collections::HashMap;

impl Solution {
    pub fn four_sum_count(nums1: Vec<i32>, nums2: Vec<i32>, nums3: Vec<i32>, nums4: Vec<i32>) -> i32 {
        // 折半：先统计 nums1+nums2 的所有和及出现次数，
        // 再枚举 nums3+nums4，累加其相反数在表中的计数。
        // 时间复杂度 O(n^2)，空间 O(n^2)。
        let mut cnt: HashMap<i32, i32> = HashMap::with_capacity(nums1.len() * nums2.len());
        for &x in &nums1 {
            for &y in &nums2 {
                *cnt.entry(x + y).or_insert(0) += 1;
            }
        }

        let mut ans = 0;
        for &x in &nums3 {
            for &y in &nums4 {
                ans += cnt.get(&(-x - y)).copied().unwrap_or(0);
            }
        }
        ans
    }
}
```

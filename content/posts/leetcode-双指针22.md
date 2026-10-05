---
title: "leetcode-双指针22"
date: 2026-09-05T09:53:02+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 两个数组的交集

给定两个数组 nums1 和 nums2 ，返回 它们的 交集 。输出结果中的每个元素一定是 唯一 的。我们可以 不考虑输出结果的顺序 。


```
use std::collections::HashSet;

impl Solution {
    pub fn intersection(nums1: Vec<i32>, nums2: Vec<i32>) -> Vec<i32> {
        // 将较短的数组构建为哈希集合，降低内存占用
        let (mut set, other) = if nums1.len() <= nums2.len() {
            (nums1.into_iter().collect::<HashSet<_>>(), nums2)
        } else {
            (nums2.into_iter().collect::<HashSet<_>>(), nums1)
        };

        // 遍历另一个数组，仅当元素存在于集合中时收集（移除确保唯一性）
        other.into_iter().filter(|x| set.remove(x)).collect()
    }
}
```

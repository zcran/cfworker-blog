---
title: "leetcode-哈希表65"
date: 2026-10-09T20:07:59+08:00
tags: ["leetcode", "哈希表"]
draft: false
---


## 找到两个数组中的公共元素

给你两个下标从 0 开始的整数数组 nums1 和 nums2 ，它们分别含有 n 和 m 个元素。请你计算以下两个数值：

answer1：使得 nums1[i] 在 nums2 中出现的下标 i 的数量。
answer2：使得 nums2[i] 在 nums1 中出现的下标 i 的数量。

返回 [answer1, answer2]。


```
use std::collections::HashSet;

impl Solution {
    pub fn find_intersection_values(nums1: Vec<i32>, nums2: Vec<i32>) -> Vec<i32> {
        // 用集合加速"是否存在于另一个数组"的查询
        let s1: HashSet<i32> = nums1.iter().copied().collect();
        let s2: HashSet<i32> = nums2.iter().copied().collect();

        // 统计每个数组中，其元素出现在另一个数组里的下标数量
        let ans1 = nums1.iter().filter(|x| s2.contains(x)).count() as i32;
        let ans2 = nums2.iter().filter(|x| s1.contains(x)).count() as i32;

        vec![ans1, ans2]
    }
}
```

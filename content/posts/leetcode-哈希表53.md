---
title: "leetcode-哈希表53"
date: 2026-10-09T20:07:58+08:00
tags: ["leetcode", "哈希表"]
draft: false
---


## 找出两数组的不同

给你两个下标从 0 开始的整数数组 nums1 和 nums2 ，请你返回一个长度为 2 的列表 answer ，其中：

answer[0] 是 nums1 中所有 不 存在于 nums2 中的 不同 整数组成的列表。
answer[1] 是 nums2 中所有 不 存在于 nums1 中的 不同 整数组成的列表。

注意：列表中的整数可以按 任意 顺序返回。


```
use std::collections::HashSet;

impl Solution {
    pub fn find_difference(nums1: Vec<i32>, nums2: Vec<i32>) -> Vec<Vec<i32>> {
        // 去重后便于做集合差运算
        let s1: HashSet<i32> = nums1.into_iter().collect();
        let s2: HashSet<i32> = nums2.into_iter().collect();

        // 分别求两个方向的差集
        vec![
            s1.difference(&s2).copied().collect(),
            s2.difference(&s1).copied().collect(),
        ]
    }
}
```

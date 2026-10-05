---
title: "leetcode-位运算34"
date: 2026-09-24T10:33:36+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 至少在两个数组中出现的值

给你三个整数数组 nums1、nums2 和 nums3 ，请你构造并返回一个 元素各不相同的 数组，且由 至少 在 两个 数组中出现的所有值组成。数组中的元素可以按 任意 顺序排列。


```
use std::collections::HashSet;

impl Solution {
    /// 返回至少在两个数组中出现过的所有不同元素。
    ///
    /// 先将每个数组去重，再求两两交集并合并到同一个集合中，
    /// 集合自动去重，最后收集为 Vec。
    pub fn two_out_of_three(nums1: Vec<i32>, nums2: Vec<i32>, nums3: Vec<i32>) -> Vec<i32> {
        let s1: HashSet<i32> = nums1.into_iter().collect();
        let s2: HashSet<i32> = nums2.into_iter().collect();
        let s3: HashSet<i32> = nums3.into_iter().collect();

        let mut res = HashSet::new();
        res.extend(s1.intersection(&s2).copied());
        res.extend(s2.intersection(&s3).copied());
        res.extend(s3.intersection(&s1).copied());

        res.into_iter().collect()
    }
}
```

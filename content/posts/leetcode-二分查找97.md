---
title: "leetcode-二分查找97"
date: 2026-09-28T12:29:17+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 搜索插入位置

给定一个排序的整数数组 nums 和一个整数目标值 target ，请在数组中找到 target ，并返回其下标。如果目标值不存在于数组中，返回它将会被按顺序插入的位置。

请必须使用时间复杂度为 O(log n) 的算法。


```
impl Solution {
    pub fn search_insert(nums: Vec<i32>, target: i32) -> i32 {
        // binary_search 返回 Ok(i) 表示找到，Err(i) 表示应插入的位置
        nums.binary_search(&target).unwrap_or_else(|i| i) as i32
    }
}
```

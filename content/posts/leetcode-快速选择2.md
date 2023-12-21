---
title: "Leetcode 快速选择2"
date: 2023-12-19T15:49:31+08:00
tags: ["leetcode", "快速选择"]
draft: false
---

## 数组中的第K个最大元素

给定整数数组 nums 和整数 k，请返回数组中第 k 个最大的元素。

请注意，你需要找的是数组排序后的第 k 个最大的元素，而不是第 k 个不同的元素。

你必须设计并实现时间复杂度为 O(n) 的算法解决此问题。

```
use std::collections::BinaryHeap;
impl Solution {
    pub fn find_kth_largest(nums: Vec<i32>, k: i32) -> i32 {
        let mut h = BinaryHeap::from(nums.iter().map(|&i| -i).take(k as usize).collect::<Vec<_>>());
        for i in k as usize..nums.len() {
            h.push(-nums[i]);
            h.pop();
        }
        -h.peek().unwrap()
    }
}
```
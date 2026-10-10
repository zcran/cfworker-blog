---
title: "leetcode-数组7"
date: 2026-10-10T20:38:40+08:00
tags: ["leetcode", "数组"]
draft: false
---


## 最长连续递增序列

给定一个未经排序的整数数组，找到最长且 连续递增的子序列，并返回该序列的长度。

连续递增的子序列 可以由两个下标 l 和 r（l < r）确定，如果对于每个 l <= i < r，都有 nums[i] < nums[i + 1] ，那么子序列 [nums[l], nums[l + 1], ..., nums[r - 1], nums[r]] 就是连续递增子序列。


```
impl Solution {
    /// 求最长连续递增子序列的长度。
    ///
    /// 一次遍历，若 `nums[i] > nums[i-1]` 则当前长度加一，
    /// 否则当前长度重置为 1；过程中维护历史最大值。
    ///
    /// 时间复杂度 O(n)，空间复杂度 O(1)。
    pub fn find_length_of_lcis(nums: Vec<i32>) -> i32 {
        let mut max_len = 1;
        let mut cur_len = 1;

        for i in 1..nums.len() {
            cur_len = if nums[i] > nums[i - 1] { cur_len + 1 } else { 1 };
            max_len = max_len.max(cur_len);
        }

        max_len
    }
}
```

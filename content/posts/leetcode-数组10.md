---
title: "leetcode-数组10"
date: 2026-10-10T20:38:41+08:00
tags: ["leetcode", "数组"]
draft: false
---


## 单调数列

如果数组是单调递增或单调递减的，那么它是 单调 的。

如果对于所有 i <= j，nums[i] <= nums[j]，那么数组 nums 是单调递增的。 如果对于所有 i <= j，nums[i] >= nums[j]，那么数组 nums 是单调递减的。

当给定的数组 nums 是单调数组时返回 true，否则返回 false。


```
impl Solution {
    /// 判断数组是否单调（递增或递减）。
    ///
    /// 一次遍历，同时用两个标志位记录是否可能递增、是否可能递减。
    /// 遇到 `nums[i] < nums[i-1]` 则递增标志失效；
    /// 遇到 `nums[i] > nums[i-1]` 则递减标志失效。
    /// 遍历结束后只要有一个标志仍成立，即为单调数组。
    ///
    /// 时间复杂度 O(n)，空间复杂度 O(1)。
    pub fn is_monotonic(nums: Vec<i32>) -> bool {
        let mut increasing = true;
        let mut decreasing = true;

        for i in 1..nums.len() {
            if nums[i] < nums[i - 1] {
                increasing = false;
            }
            if nums[i] > nums[i - 1] {
                decreasing = false;
            }
        }

        increasing || decreasing
    }
}
```

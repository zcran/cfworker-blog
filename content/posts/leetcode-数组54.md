---
title: "leetcode-数组54"
date: 2026-10-10T20:38:44+08:00
tags: ["leetcode", "数组"]
draft: false
---


## 统计符合条件长度为 3 的子数组数目

给你一个整数数组 nums ，请你返回长度为 3 的 子数组 的数量，满足第一个数和第三个数的和恰好为第二个数的一半。

子数组 指的是一个数组中连续 非空 的元素序列。


```
impl Solution {
    /// 统计长度为 3 的子数组数量，满足首尾之和恰好为中间数的一半。
    ///
    /// 条件 `nums[i-1] + nums[i+1] == nums[i] / 2` 两边乘 2，
    /// 等价于 `(nums[i-1] + nums[i+1]) * 2 == nums[i]`。
    ///
    /// 时间复杂度 O(n)，空间复杂度 O(1)。
    pub fn count_subarrays(nums: Vec<i32>) -> i32 {
        let mut count = 0;
        for i in 1..nums.len() - 1 {
            if (nums[i - 1] + nums[i + 1]) * 2 == nums[i] {
                count += 1;
            }
        }
        count
    }
}
```

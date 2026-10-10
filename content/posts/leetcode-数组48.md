---
title: "leetcode-数组48"
date: 2026-10-10T20:38:43+08:00
tags: ["leetcode", "数组"]
draft: false
---


## 最长的严格递增或递减子数组

给你一个整数数组 nums 。

返回数组 nums 中 严格递增 或 严格递减 的最长非空子数组的长度。


```
impl Solution {
    /// 返回数组中最长的严格递增或严格递减连续子数组的长度。
    ///
    /// 一次遍历，用 `inc` / `dec` 分别记录以当前位置结尾的
    /// 严格递增、严格递减连续长度：
    /// - 若 `nums[i] > nums[i-1]`，`inc += 1`，`dec = 1`；
    /// - 若 `nums[i] < nums[i-1]`，`dec += 1`，`inc = 1`；
    /// - 否则两者都重置为 1。
    /// 每轮用 `inc` / `dec` 更新答案。
    ///
    /// 时间复杂度 O(n)，空间复杂度 O(1)。
    pub fn longest_monotonic_subarray(nums: Vec<i32>) -> i32 {
        let mut max_len = 1;
        let mut inc = 1;
        let mut dec = 1;

        for i in 1..nums.len() {
            if nums[i] > nums[i - 1] {
                inc += 1;
                dec = 1;
            } else if nums[i] < nums[i - 1] {
                dec += 1;
                inc = 1;
            } else {
                inc = 1;
                dec = 1;
            }
            max_len = max_len.max(inc).max(dec);
        }

        max_len
    }
}
```

---
title: "leetcode-数组11"
date: 2026-10-10T20:38:41+08:00
tags: ["leetcode", "数组"]
draft: false
---


## 分割数组

给定一个数组 nums ，将其划分为两个连续子数组 left 和 right， 使得：

left 中的每个元素都小于或等于 right 中的每个元素。
left 和 right 都是非空的。
left 的长度要尽可能小。

在完成这样的分组后返回 left 的 长度 。

用例可以保证存在这样的划分方法。


```
impl Solution {
    /// 将数组划分为两个连续子数组，使左子数组所有元素 ≤ 右子数组所有元素，
    /// 且左子数组尽可能短，返回左子数组长度。
    ///
    /// 一次遍历：维护左子数组最大值 `left_max` 与当前前缀最大值 `cur_max`。
    /// - 若 `nums[i] < left_max`，说明 `nums[i]` 必须归入左子数组，
    ///   于是把划分点扩展到 `i + 1`，并令 `left_max = cur_max`；
    /// - 否则更新 `cur_max`。
    ///
    /// 时间复杂度 O(n)，空间复杂度 O(1)。
    pub fn partition_disjoint(nums: Vec<i32>) -> i32 {
        let mut partition = 1;
        let mut left_max = nums[0];
        let mut cur_max = nums[0];

        for i in 1..nums.len() {
            if nums[i] < left_max {
                partition = i + 1;
                left_max = cur_max;
            } else {
                cur_max = cur_max.max(nums[i]);
            }
        }

        partition as i32
    }
}
```

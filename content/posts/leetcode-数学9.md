---
title: "leetcode-数学9"
date: 2026-10-07T20:39:28+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 最小差值 I

给你一个整数数组 nums，和一个整数 k 。

在一个操作中，您可以选择 0 <= i < nums.length 的任何索引 i 。将 nums[i] 改为 nums[i] + x ，其中 x 是一个范围为 [-k, k] 的任意整数。对于每个索引 i ，最多 只能 应用 一次 此操作。

nums 的 分数 是 nums 中最大和最小元素的差值。

在对  nums 中的每个索引最多应用一次上述操作后，返回 nums 的最低 分数 。


```
impl Solution {
    /// 对每个元素最多加/减一次（范围 [-k, k]）后，返回数组的最小极差。
    ///
    /// 记原数组最小值为 mn、最大值为 mx。
    /// 为了让极差最小：
    ///   - 最小值 mn 最多能增加到 mn + k；
    ///   - 最大值 mx 最多能减少到 mx - k。
    /// 因此可以达到的最小极差为 max(0, (mx - k) - (mn + k)) = max(0, mx - mn - 2k)。
    /// 若 2k 已覆盖原极差，则可让所有元素相等，极差为 0。
    pub fn smallest_range_i(nums: Vec<i32>, k: i32) -> i32 {
        let mn = *nums.iter().min().unwrap();
        let mx = *nums.iter().max().unwrap();
        (mx - mn - 2 * k).max(0)
    }
}
```

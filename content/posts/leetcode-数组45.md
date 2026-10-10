---
title: "leetcode-数组45"
date: 2026-10-10T20:38:43+08:00
tags: ["leetcode", "数组"]
draft: false
---


## 元素和最小的山形三元组 I

给你一个下标从 0 开始的整数数组 nums 。

如果下标三元组 (i, j, k) 满足下述全部条件，则认为它是一个 山形三元组 ：

i < j < k
nums[i] < nums[j] 且 nums[k] < nums[j]

请你找出 nums 中 元素和最小 的山形三元组，并返回其 元素和 。如果不存在满足条件的三元组，返回 -1 。


```
impl Solution {
    /// 求元素和最小的山形三元组，不存在则返回 -1。
    ///
    /// 固定中间下标 `j`，两侧各取严格小于 `nums[j]` 的最小值：
    /// `min_left[j] = min(nums[..j])`，`min_right[j] = min(nums[j+1..])`。
    /// 若两侧最小值都严格小于 `nums[j]`，候选和为三者之和。
    ///
    /// 时间复杂度 O(n)，空间复杂度 O(n)。
    pub fn minimum_sum(nums: Vec<i32>) -> i32 {
        let n = nums.len();
        if n < 3 {
            return -1;
        }

        // left_min[j] = min(nums[0..j])，即 j 左侧的最小值
        let mut left_min = vec![i32::MAX; n];
        for j in 1..n {
            left_min[j] = left_min[j - 1].min(nums[j - 1]);
        }

        // right_min[j] = min(nums[j+1..])，即 j 右侧的最小值
        let mut right_min = vec![i32::MAX; n];
        for j in (0..n - 1).rev() {
            right_min[j] = right_min[j + 1].min(nums[j + 1]);
        }

        // 枚举中间下标 j，两侧必须都严格小于 nums[j]
        let mut best = i32::MAX;
        for j in 1..n - 1 {
            let (l, r) = (left_min[j], right_min[j]);
            if l < nums[j] && r < nums[j] {
                best = best.min(l + nums[j] + r);
            }
        }

        if best == i32::MAX { -1 } else { best }
    }
}
```

---
title: "leetcode-数组44"
date: 2026-10-10T20:38:43+08:00
tags: ["leetcode", "数组"]
draft: false
---


## 有序三元组中的最大值 II

给你一个下标从 0 开始的整数数组 nums 。

请你从所有满足 i < j < k 的下标三元组 (i, j, k) 中，找出并返回下标三元组的最大值。如果所有满足条件的三元组的值都是负数，则返回 0 。

下标三元组 (i, j, k) 的值等于 (nums[i] - nums[j]) * nums[k] 。


```
impl Solution {
    /// 求满足 `i < j < k` 的三元组 `(nums[i] - nums[j]) * nums[k]` 的最大值，全负则返回 0。
    ///
    /// 固定 `j`，表达式可拆为 `(max_{i<j} nums[i] - nums[j]) * max_{k>j} nums[k]`。
    /// 先预处理前缀最大值 `left_max[j] = max(nums[..j])`
    /// 与后缀最大值 `right_max[j] = max(nums[j+1..])`，
    /// 再枚举 `j` 用 O(1) 计算候选值。
    ///
    /// 时间复杂度 O(n)，空间复杂度 O(n)。
    pub fn maximum_triplet_value(nums: Vec<i32>) -> i64 {
        let n = nums.len();
        if n < 3 {
            return 0;
        }

        // left_max[j] = max(nums[0..j])，即 j 左侧的最大值
        let mut left_max = vec![0i32; n];
        for j in 1..n {
            left_max[j] = left_max[j - 1].max(nums[j - 1]);
        }

        // right_max[j] = max(nums[j+1..])，即 j 右侧的最大值
        let mut right_max = vec![0i32; n];
        for j in (0..n - 1).rev() {
            right_max[j] = right_max[j + 1].max(nums[j + 1]);
        }

        // 枚举中间下标 j
        let mut best = 0i64;
        for j in 1..n - 1 {
            let candidate = (left_max[j] as i64 - nums[j] as i64) * right_max[j] as i64;
            best = best.max(candidate);
        }

        best
    }
}
```

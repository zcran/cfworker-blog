---
title: "leetcode-数组43"
date: 2026-10-10T20:38:43+08:00
tags: ["leetcode", "数组"]
draft: false
---


## 有序三元组中的最大值 I

给你一个下标从 0 开始的整数数组 nums 。

请你从所有满足 i < j < k 的下标三元组 (i, j, k) 中，找出并返回下标三元组的最大值。如果所有满足条件的三元组的值都是负数，则返回 0 。

下标三元组 (i, j, k) 的值等于 (nums[i] - nums[j]) * nums[k] 。


```
impl Solution {
    /// 求满足 `i < j < k` 的三元组 `(nums[i] - nums[j]) * nums[k]` 的最大值，全负则返回 0。
    ///
    /// 固定 `j`，枚举其左侧的 `i` 和右侧的 `k` 会达到 O(n³)。
    /// 观察表达式：对每个 `j`，需要最大化
    /// `(max_{i<j} nums[i] - nums[j]) * max_{k>j} nums[k]`。
    /// 可先预处理后缀最大值 `suf_max[j] = max(nums[j..])`，
    /// 再从左到右维护前缀最大值 `pre_max`，对每个 `j` 用 O(1) 计算候选值。
    ///
    /// 时间复杂度 O(n)，空间复杂度 O(n)（后缀最大值数组）。
    pub fn maximum_triplet_value(nums: Vec<i32>) -> i64 {
        let n = nums.len();
        if n < 3 {
            return 0;
        }

        // suf_max[j] = max(nums[j..])
        let mut suf_max = vec![0i32; n];
        suf_max[n - 1] = nums[n - 1];
        for j in (0..n - 1).rev() {
            suf_max[j] = suf_max[j + 1].max(nums[j]);
        }

        let mut best = 0i64;
        let mut pre_max = nums[0]; // max(nums[..j])

        // 枚举中间下标 j，i < j < k
        for j in 1..n - 1 {
            let candidate = (pre_max as i64 - nums[j] as i64) * suf_max[j + 1] as i64;
            best = best.max(candidate);
            pre_max = pre_max.max(nums[j]);
        }

        best
    }
}
```

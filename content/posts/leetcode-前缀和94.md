---
title: "leetcode-前缀和94"
date: 2026-08-25T10:02:51+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 最大好子数组和

给你一个长度为 n 的数组 nums 和一个 正 整数 k 。

如果 nums 的一个子数组中，第一个元素和最后一个元素 差的绝对值恰好 为 k ，我们称这个子数组为 好 的。换句话说，如果子数组 nums[i..j] 满足 |nums[i] - nums[j]| == k ，那么它是一个好子数组。

请你返回 nums 中 好 子数组的 最大 和，如果没有好子数组，返回 0 。


```
impl Solution {
    pub fn maximum_subarray_sum(nums: Vec<i32>, k: i32) -> i64 {
        use std::collections::HashMap;

        // 记录每个值作为子数组起点时，能获得的最小前缀和
        // 对于值 v，我们希望找到以 v 开头、以 v±k 结尾的最大子数组和
        let mut best_start = HashMap::new();
        let mut prefix_sum = 0i64;
        let mut ans = i64::MIN;

        for &x in &nums {
            // 当前元素 x 可以作为终点，找起点为 x-k 或 x+k 的情况
            for &target in &[x - k, x + k] {
                if let Some(&min_prefix) = best_start.get(&target) {
                    // 子数组和为 当前前缀和 - 之前的最小前缀和
                    ans = ans.max(prefix_sum + x as i64 - min_prefix);
                }
            }

            // 更新以 x 为起点的最小前缀和
            // 前缀和更新前是子数组 nums[0..i-1] 的和
            // 如果以 x 为起点，前缀和应该是之前的前缀和
            let entry = best_start.entry(x).or_insert(i64::MAX);
            *entry = (*entry).min(prefix_sum);

            prefix_sum += x as i64;
        }

        if ans == i64::MIN { 0 } else { ans }
    }
}
```

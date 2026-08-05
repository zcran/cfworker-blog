---
title: "leetcode-滑动窗口104"
date: 2026-07-18T11:02:35+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 执行操作后元素的最高频率 II

给你一个整数数组 nums 和两个整数 k 和 numOperations 。

你必须对 nums 执行 操作  numOperations 次。每次操作中，你可以：

选择一个下标 i ，它在之前的操作中 没有 被选择过。
将 nums[i] 增加范围 [-k, k] 中的一个整数。

在执行完所有操作以后，请你返回 nums 中出现 频率最高 元素的出现次数。

一个元素 x 的 频率 指的是它在数组中出现的次数。


```
use std::collections::{HashMap, HashSet};

impl Solution {
    pub fn max_frequency(nums: Vec<i32>, k: i32, num_operations: i32) -> i32 {
        let mut nums = nums;
        nums.sort_unstable();
        let n = nums.len();

        // 统计每个原始值的出现次数
        let mut freq = HashMap::new();
        let mut candidates = HashSet::new(); // 可能成为最终目标的值

        let mut start = 0;
        for i in 0..n {
            if nums[i] != nums[start] {
                let count = i - start;
                freq.insert(nums[start], count);
                candidates.insert(nums[start]);
                candidates.insert(nums[start] - k);
                candidates.insert(nums[start] + k);
                start = i;
            }
        }
        let count = n - start;
        freq.insert(nums[start], count);
        candidates.insert(nums[start]);
        candidates.insert(nums[start] - k);
        candidates.insert(nums[start] + k);

        let mut ans = freq.values().max().copied().unwrap_or(0) as i32;

        // 二分查找：第一个 >= target 的位置
        let lower_bound = |target: i32| -> usize {
            let (mut lo, mut hi) = (0, n);
            while lo < hi {
                let mid = (lo + hi) / 2;
                if nums[mid] < target {
                    lo = mid + 1;
                } else {
                    hi = mid;
                }
            }
            lo
        };

        // 二分查找：最后一个 <= target 的位置
        let upper_bound = |target: i32| -> usize {
            let (mut lo, mut hi) = (0, n);
            while lo < hi {
                let mid = (lo + hi) / 2;
                if nums[mid] <= target {
                    lo = mid + 1;
                } else {
                    hi = mid;
                }
            }
            lo - 1
        };

        // 只检查候选值，避免遍历整个值域
        let mut sorted_candidates: Vec<_> = candidates.into_iter().collect();
        sorted_candidates.sort_unstable();

        for &target in &sorted_candidates {
            // 范围 [target - k, target + k] 内的元素都可以变成 target
            let left = lower_bound(target - k);
            let right = upper_bound(target + k);

            let total_in_range = right - left + 1;
            let existing = *freq.get(&target).unwrap_or(&0);

            // 已有的 target 保留，其余元素最多选 num_operations 个操作
            let can_achieve = if existing > 0 {
                (total_in_range - existing).min(num_operations as usize) + existing
            } else {
                total_in_range.min(num_operations as usize)
            };

            ans = ans.max(can_achieve as i32);
        }

        ans
    }
}
```

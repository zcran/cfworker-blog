---
title: "leetcode-滑动窗口103"
date: 2026-07-18T11:02:35+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 执行操作后元素的最高频率 I

给你一个整数数组 nums 和两个整数 k 和 numOperations 。

你必须对 nums 执行 操作  numOperations 次。每次操作中，你可以：

选择一个下标 i ，它在之前的操作中 没有 被选择过。
将 nums[i] 增加范围 [-k, k] 中的一个整数。
在执行完所有操作以后，请你返回 nums 中出现 频率最高 元素的出现次数。

一个元素 x 的 频率 指的是它在数组中出现的次数。


```
use std::collections::HashMap;

impl Solution {
    pub fn max_frequency(nums: Vec<i32>, k: i32, num_operations: i32) -> i32 {
        // 1. 排序数组，便于统计频率和二分查找
        let mut nums = nums;
        nums.sort_unstable();
        let n = nums.len();

        // 2. 统计每个不同元素的出现次数，并记录最大频率
        let mut freq = HashMap::new();
        let mut max_original_freq = 0;

        let mut start = 0;
        for i in 0..n {
            if nums[i] != nums[start] {
                let count = (i - start) as i32;
                freq.insert(nums[start], count);
                max_original_freq = max_original_freq.max(count);
                start = i;
            }
        }
        let count = (n - start) as i32;
        freq.insert(nums[start], count);
        max_original_freq = max_original_freq.max(count);

        // 3. 二分查找：找到第一个 >= value 的位置
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

        // 4. 二分查找：找到最后一个 <= value 的位置
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
            lo - 1 // 返回最后一个 <= target 的索引
        };

        // 5. 枚举所有可能的目标值（只枚举数组值域内的整数）
        let mut ans = max_original_freq;
        for target in nums[0]..=nums[n - 1] {
            // 能够通过操作变成 target 的元素范围：[target - k, target + k]
            let left = lower_bound(target - k);
            let right = upper_bound(target + k);

            // 区间内可操作的元素总数
            let available = (right - left + 1) as i32;

            // 区间内已经是 target 的元素数量（不需要操作）
            let existing = freq.get(&target).copied().unwrap_or(0);

            // 计算最终能变成 target 的最大数量：
            // - 已有的 target 元素保留
            // - 其余元素中最多选 num_operations 个进行操作
            let can_become = if existing > 0 {
                // 已有 target 占据了一些位置，但不需要操作
                // 可操作的元素是区间内除已有 target 外的元素
                (available - existing).min(num_operations) + existing
            } else {
                // 区间内没有 target，最多操作 num_operations 个
                available.min(num_operations)
            };

            ans = ans.max(can_become);
        }

        ans
    }
}
```

---
title: "leetcode-滑动窗口80"
date: 2026-07-18T11:02:33+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 执行操作使频率分数最大

给你一个下标从 0 开始的整数数组 nums 和一个整数 k 。

你可以对数组执行 至多 k 次操作：

从数组中选择一个下标 i ，将 nums[i] 增加 或者 减少 1 。

最终数组的频率分数定义为数组中众数的 频率 。

请你返回你可以得到的 最大 频率分数。

众数指的是数组中出现次数最多的数。一个元素的频率指的是数组中这个元素的出现次数。


```
impl Solution {
    pub fn max_frequency_score(mut nums: Vec<i32>, k: i64) -> i32 {
        nums.sort_unstable();
        let n = nums.len();
        let mut left = 0;
        let mut cost = 0i64; // 将 nums[left..=right] 变为同一值的最小代价
        let mut max_freq = 1;

        // 滑动窗口，维护窗口内所有元素变为中位数的最小代价
        for right in 0..n {
            // 将新元素 nums[right] 变为 nums[(left+right)/2] 的代价
            let mid = (left + right) / 2;
            cost += (nums[right] - nums[mid]) as i64;

            // 如果代价超过 k，收缩左边界
            while cost > k {
                // 将 nums[left] 从窗口移除，更新代价
                let mid = (left + right + 1) / 2;
                cost -= (nums[mid] - nums[left]) as i64;
                left += 1;
            }

            max_freq = max_freq.max(right - left + 1);
        }

        max_freq as i32
    }
}
```

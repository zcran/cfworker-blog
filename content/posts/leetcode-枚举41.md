---
title: "leetcode-枚举41"
date: 2026-07-09T10:05:01+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 最长交替子数组

给你一个下标从 0 开始的整数数组 nums 。如果 nums 中长度为 m 的子数组 s 满足以下条件，我们称它是一个 交替子数组 ：

· m 大于 1 。
· s1 = s0 + 1 。
· 下标从 0 开始的子数组 s 与数组 [s0, s1, s0, s1,...,s(m-1) % 2] 一样。也就是说，s1 - s0 = 1 ，s2 - s1 = -1 ，s3 - s2 = 1 ，s4 - s3 = -1 ，以此类推，直到 s[m - 1] - s[m - 2] = (-1)m 。

请你返回 nums 中所有 交替 子数组中，最长的长度，如果不存在交替子数组，请你返回 -1 。

子数组是一个数组中一段连续 非空 的元素序列。


```
impl Solution {
    pub fn alternating_subarray(nums: Vec<i32>) -> i32 {
        let n = nums.len();
        let mut max_len = -1;

        // 枚举每个可能的起点
        for start in 0..n {
            // 从 start+1 开始扩展
            let mut curr_len = 1;
            // 期望的下一个差值：+1, -1, +1, -1, ...
            let mut expected_diff = 1;

            for end in start + 1..n {
                let actual_diff = nums[end] - nums[end - 1];

                // 检查当前差值是否符合期望
                if actual_diff != expected_diff {
                    break;
                }

                curr_len += 1;
                max_len = max_len.max(curr_len);

                // 交替期望值：+1 变 -1，-1 变 +1
                expected_diff = -expected_diff;
            }
        }

        max_len
    }
}
```

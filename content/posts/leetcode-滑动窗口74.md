---
title: "leetcode-滑动窗口74"
date: 2026-07-18T11:02:33+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 无限数组的最短子数组

给你一个下标从 0 开始的数组 nums 和一个整数 target 。

下标从 0 开始的数组 infinite_nums 是通过无限地将 nums 的元素追加到自己之后生成的。

请你从 infinite_nums 中找出满足 元素和 等于 target 的 最短 子数组，并返回该子数组的长度。如果不存在满足条件的子数组，返回 -1 。


```
impl Solution {
    pub fn min_size_subarray(nums: Vec<i32>, target: i32) -> i32 {
        let target = target as i64;
        let n = nums.len();
        let total_sum: i64 = nums.iter().map(|&x| x as i64).sum();

        // 如果 target 比整个数组总和大，需要先去掉整数个完整数组
        let full_cycles = target / total_sum;
        let remain = (target % total_sum) as i32;

        // 如果余数为 0，直接返回完整数组的个数 × n
        if remain == 0 {
            return (full_cycles * n as i64) as i32;
        }

        // 在长度为 2n 的重复数组中寻找和为 remain 的最短子数组
        let extended_len = 2 * n;
        let mut window_sum = 0;
        let mut left = 0;
        let mut min_len = usize::MAX;

        for right in 0..extended_len {
            window_sum += nums[right % n];

            // 收缩窗口直到和 <= remain
            while window_sum > remain {
                window_sum -= nums[left % n];
                left += 1;
            }

            // 如果找到和等于 remain 的子数组
            if window_sum == remain {
                let len = right - left + 1;
                // 长度不能超过 n（因为 remain < total_sum，最优解不会超过 n）
                if len <= n {
                    min_len = min_len.min(len);
                }
            }
        }

        if min_len == usize::MAX {
            -1
        } else {
            (full_cycles * n as i64 + min_len as i64) as i32
        }
    }
}
```

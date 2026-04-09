---
title: "leetcode-单调队列15"
date: 2026-03-31T20:55:29+08:00
tags: ["leetcode", "单调队列"]
draft: false
---


## 找到最大非递减数组的长度

给你一个下标从 0 开始的整数数组 nums 。

你可以执行任意次操作。每次操作中，你需要选择一个 子数组 ，并将这个子数组用它所包含元素的 和 替换。比方说，给定数组是 [1,3,5,6] ，你可以选择子数组 [3,5] ，用子数组的和 8 替换掉子数组，然后数组会变为 [1,8,6] 。

请你返回执行任意次操作以后，可以得到的 最长非递减 数组的长度。

子数组 指的是一个数组中一段连续 非空 的元素序列。

```
use std::collections::VecDeque;

impl Solution {
    pub fn find_maximum_length(nums: Vec<i32>) -> i32 {
        let mut prefix = 0i64;
        // (前缀和, 下段最小阈值, 分段数)
        let mut seg = VecDeque::from([(0, 0, 0)]);

        for &x in &nums {
            prefix += x as i64;

            // 移除无用状态：只保留最后一个阈值 <= prefix 的
            let last_valid = seg.partition_point(|&(_, t, _)| t <= prefix);
            if last_valid > 1 {
                seg.drain(..last_valid - 1);
            }

            let (prev_sum, _, prev_cnt) = seg[0];
            let next_th = 2 * prefix - prev_sum;
            let next_cnt = prev_cnt + 1;

            // 维护阈值递增
            while let Some(&(_, last_th, _)) = seg.back() {
                if last_th < next_th {
                    break;
                }
                seg.pop_back();
            }
            seg.push_back((prefix, next_th, next_cnt));
        }

        seg.back().unwrap().2
    }
}
```

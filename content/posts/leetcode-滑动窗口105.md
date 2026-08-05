---
title: "leetcode-滑动窗口105"
date: 2026-07-18T11:02:35+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 最小正和子数组

给你一个整数数组 nums 和 两个 整数 l 和 r。你的任务是找到一个长度在 l 和 r 之间（包含）且和大于 0 的 子数组 的 最小 和。

返回满足条件的子数组的 最小 和。如果不存在这样的子数组，则返回 -1。

子数组 是数组中的一个连续 非空 元素序列。


```
impl Solution {
    pub fn minimum_sum_subarray(nums: Vec<i32>, l: i32, r: i32) -> i32 {
        let (l, r) = (l as usize, r as usize);
        let n = nums.len();
        let mut ans = i32::MAX;

        // 枚举所有可能的子数组长度
        for len in l..=r {
            if len > n {
                break;
            }

            // 滑动窗口计算长度为 len 的子数组和
            let mut window_sum: i32 = nums[..len].iter().sum();
            if window_sum > 0 {
                ans = ans.min(window_sum);
            }

            for i in len..n {
                window_sum += nums[i] - nums[i - len];
                if window_sum > 0 {
                    ans = ans.min(window_sum);
                }
            }
        }

        if ans == i32::MAX { -1 } else { ans }
    }
}
```

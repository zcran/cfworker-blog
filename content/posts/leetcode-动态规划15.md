---
title: "leetcode-动态规划15"
date: 2026-10-05T20:39:54+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 打家劫舍 II

你是一个专业的小偷，计划偷窃沿街的房屋，每间房内都藏有一定的现金。这个地方所有的房屋都 围成一圈 ，这意味着第一个房屋和最后一个房屋是紧挨着的。同时，相邻的房屋装有相互连通的防盗系统，如果两间相邻的房屋在同一晚上被小偷闯入，系统会自动报警 。

给定一个代表每个房屋存放金额的非负整数数组，计算你 在不触动警报装置的情况下 ，今晚能够偷窃到的最高金额。


```
impl Solution {
    pub fn rob(nums: Vec<i32>) -> i32 {
        // 在 [start, end) 范围内偷窃，返回最大金额
        fn rob_range(nums: &[i32], start: usize, end: usize) -> i32 {
            let (mut f0, mut f1) = (0, 0);
            for &x in &nums[start..end] {
                // f0: 不偷当前房；f1: 偷当前房
                let cur = f1.max(f0 + x);
                f0 = f1;
                f1 = cur;
            }
            f1
        }

        let n = nums.len();
        // 房屋数不超过 2 时，首尾相邻的限制不会产生额外冲突，直接取最大
        if n == 1 {
            return nums[0];
        }
        if n == 2 {
            return nums[0].max(nums[1]);
        }

        // 首尾不能同时偷，分两种情况取最大：
        //   情况一：不偷第一间，考虑 [1, n)
        //   情况二：偷第一间，则最后一间不能偷，考虑 [2, n-1)
        rob_range(&nums, 1, n).max(nums[0] + rob_range(&nums, 2, n - 1))
    }
}
```

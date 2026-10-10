---
title: "leetcode-动态规划168"
date: 2026-10-05T20:40:05+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 打家劫舍 V

你是一名专业小偷，计划偷窃沿街的房屋。每间房屋都藏有一定的现金，并由带有颜色代码的安全系统保护。

给你两个长度为 n 的整数数组 nums 和 colors，其中 nums[i] 是第 i 间房屋中的金额，而 colors[i] 是该房屋的颜色代码。

如果两间 相邻 的房屋具有 相同 的颜色代码，则你 不能同时偷窃 它们。

返回你能偷窃到的 最大 金额。


```
impl Solution {
    pub fn rob(nums: Vec<i32>, colors: Vec<i32>) -> i64 {
        let n = nums.len();
        if n == 1 {
            return nums[0] as i64;
        }

        // prev2 = dp[i-2]，prev1 = dp[i-1]
        let mut prev2 = nums[0] as i64;
        let mut prev1 = if colors[1] == colors[0] {
            prev2.max(nums[1] as i64)
        } else {
            prev2 + nums[1] as i64
        };

        for i in 2..n {
            let cur = if colors[i] != colors[i - 1] {
                // 相邻颜色不同：可以偷当前，直接接在 dp[i-1] 之后
                (prev1 + nums[i] as i64).max(prev1)
            } else {
                // 相邻颜色相同：不能同时偷 i-1，只能从 dp[i-2] 转移
                (prev2 + nums[i] as i64).max(prev1)
            };
            prev2 = prev1;
            prev1 = cur;
        }

        prev1
    }
}
```

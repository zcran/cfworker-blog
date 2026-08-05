---
title: "leetcode-滑动窗口36"
date: 2026-07-18T11:02:31+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 找两个和为目标值且不重叠的子数组

给你一个整数数组 arr 和一个整数值 target 。

请你在 arr 中找 两个互不重叠的子数组 且它们的和都等于 target 。可能会有多种方案，请你返回满足要求的两个子数组长度和的 最小值 。

请返回满足要求的最小长度和，如果无法找到这样的两个子数组，请返回 -1 。


```
impl Solution {
    pub fn min_sum_of_lengths(arr: Vec<i32>, target: i32) -> i32 {
        let n = arr.len();
        // dp[i] 表示 arr[0..i) 中长度最短的和为 target 的子数组长度
        // 初始化为 n + 1 表示不存在
        let mut dp = vec![n + 1; n + 1];
        dp[0] = n + 1;

        let mut left = 0;
        let mut sum = 0;
        let mut ans = i32::MAX;

        for right in 0..n {
            sum += arr[right];

            // 收缩左边界，确保 sum <= target
            while sum > target {
                sum -= arr[left];
                left += 1;
            }

            if sum == target {
                let len = right - left + 1;
                // 当前子数组作为第二个，与之前的最短子数组组合
                if dp[left] <= n {
                    ans = ans.min((len + dp[left]) as i32);
                }
                // 更新 dp[right + 1]：取当前长度和之前最短长度的较小值
                dp[right + 1] = dp[right].min(len);
            } else {
                // 当前不是目标子数组，沿用前一个状态
                dp[right + 1] = dp[right];
            }
        }

        if ans == i32::MAX { -1 } else { ans }
    }
}
```

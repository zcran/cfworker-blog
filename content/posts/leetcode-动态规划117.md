---
title: "leetcode-动态规划117"
date: 2026-10-05T20:40:02+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 达到末尾下标所需的最大跳跃次数

给你一个下标从 0 开始、由 n 个整数组成的数组 nums 和一个整数 target 。

你的初始位置在下标 0 。在一步操作中，你可以从下标 i 跳跃到任意满足下述条件的下标 j ：

0 <= i < j < n

-target <= nums[j] - nums[i] <= target

返回到达下标 n - 1 处所需的 最大跳跃次数 。

如果无法到达下标 n - 1 ，返回 -1 。




```
impl Solution {
    pub fn maximum_jumps(nums: Vec<i32>, target: i32) -> i32 {
        let n = nums.len();
        // dp[i]: 到达下标 i 所需的最大跳跃次数，i32::MIN 表示不可达
        let mut dp = vec![i32::MIN; n];
        dp[0] = 0;

        for i in 1..n {
            for j in 0..i {
                // 只要 nums[j] 与 nums[i] 差值在 target 以内，即可从 j 跳到 i
                if dp[j] != i32::MIN && (nums[j] - nums[i]).abs() <= target {
                    dp[i] = dp[i].max(dp[j] + 1);
                }
            }
        }

        if dp[n - 1] < 0 { -1 } else { dp[n - 1] }
    }
}
```

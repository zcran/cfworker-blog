---
title: "leetcode-动态规划125"
date: 2026-10-05T20:40:02+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 和为目标值的最长子序列的长度

给你一个下标从 0 开始的整数数组 nums 和一个整数 target 。

返回和为 target 的 nums 子序列中，子序列 长度的最大值 。如果不存在和为 target 的子序列，返回 -1 。

子序列 指的是从原数组中删除一些或者不删除任何元素后，剩余元素保持原来的顺序构成的数组。


```
impl Solution {
    pub fn length_of_longest_subsequence(nums: Vec<i32>, target: i32) -> i32 {
        let target = target as usize;
        const NEG: i32 = i32::MIN / 2;

        // dp[j]: 当前已处理元素中，和为 j 的子序列最大长度
        let mut dp = vec![NEG; target + 1];
        dp[0] = 0;

        for &x in &nums {
            let x = x as usize;
            // 0-1 背包：从大到小更新，避免同一元素被重复使用
            for j in (x..=target).rev() {
                if dp[j - x] != NEG {
                    dp[j] = dp[j].max(dp[j - x] + 1);
                }
            }
        }

        if dp[target] > 0 { dp[target] } else { -1 }
    }
}
```

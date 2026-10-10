---
title: "leetcode-动态规划129"
date: 2026-10-05T20:40:02+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 求出最长好子序列 I

给你一个整数数组 nums 和一个 非负 整数 k 。如果一个整数序列 seq 满足在下标范围 [0, seq.length - 2] 中 最多只有 k 个下标 i 满足 seq[i] != seq[i + 1] ，那么我们称这个整数序列为 好 序列。

请你返回 nums 中 好 子序列 的最长长度。


```
impl Solution {
    pub fn maximum_length(nums: Vec<i32>, k: i32) -> i32 {
        let n = nums.len();
        let k = k as usize;

        // dp[i][c]: 以 nums[i] 结尾、恰好有 c 次相邻不同（c ≤ k）的好子序列最长长度
        // 用 -1 表示不可达
        let mut dp = vec![vec![-1i32; k + 1]; n];
        let mut ans = 0;

        for i in 0..n {
            dp[i][0] = 1; // 单独一个元素，0 次变化
            for j in 0..i {
                let add = (nums[i] != nums[j]) as usize; // 末尾追加 nums[i] 带来的变化次数
                // 只更新 c >= add 的状态
                for c in add..=k {
                    if dp[j][c - add] != -1 {
                        let v = dp[j][c - add] + 1;
                        if v > dp[i][c] {
                            dp[i][c] = v;
                        }
                    }
                }
            }
            // 更新答案
            for c in 0..=k {
                if dp[i][c] > ans {
                    ans = dp[i][c];
                }
            }
        }

        ans
    }
}
```

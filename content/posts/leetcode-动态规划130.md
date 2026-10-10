---
title: "leetcode-动态规划130"
date: 2026-10-05T20:40:03+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 求出最长好子序列 II

给你一个整数数组 nums 和一个 非负 整数 k 。如果一个整数序列 seq 满足在范围下标范围 [0, seq.length - 2] 中存在 不超过 k 个下标 i 满足 seq[i] != seq[i + 1] ，那么我们称这个整数序列为 好 序列。

请你返回 nums 中 好 子序列 的最长长度


```
use std::collections::HashMap;

impl Solution {
    pub fn maximum_length(nums: Vec<i32>, k: i32) -> i32 {
        let k = k as usize;

        // dp[x][c]: 以值 x 结尾、恰好用了 c 次「相邻不同」的最长好子序列长度
        let mut dp: HashMap<i32, Vec<i32>> = HashMap::new();
        // best[c]: 所有值中，用了不超过 c 次变化的最长好子序列长度
        let mut best = vec![0i32; k + 1];

        for v in nums {
            let cur = dp.entry(v).or_insert_with(|| vec![0; k + 1]);

            // 从 c = k 到 0 倒序更新，避免同一元素被重复使用
            for c in (0..=k).rev() {
                // 情况一：追加相同值 v，变化次数不变
                cur[c] += 1;
                // 情况二：从一个不同值转移而来，变化次数 +1
                if c > 0 {
                    cur[c] = cur[c].max(best[c - 1] + 1);
                }
            }

            // 更新全局最优
            for c in 0..=k {
                best[c] = best[c].max(cur[c]);
            }
        }

        best[k]
    }
}
```

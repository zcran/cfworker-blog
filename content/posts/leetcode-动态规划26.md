---
title: "leetcode-动态规划26"
date: 2026-10-05T20:39:55+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 一和零

给你一个二进制字符串数组 strs 和两个整数 m 和 n 。

请你找出并返回 strs 的最大子集的长度，该子集中 最多 有 m 个 0 和 n 个 1 。

如果 x 的所有元素也是 y 的元素，集合 x 是集合 y 的 子集 。


```
impl Solution {
    pub fn find_max_form(strs: Vec<String>, m: i32, n: i32) -> i32 {
        let (m, n) = (m as usize, n as usize);

        // dp[j][k]：在容量为 j 个 0、k 个 1 时能选出的最大子集大小
        // 使用二维 DP 原地滚动，省去"考虑前 i 个字符串"那一维
        let mut dp = vec![vec![0i32; n + 1]; m + 1];

        for s in &strs {
            // 统计当前字符串的 0 和 1 的个数
            let zeros = s.bytes().filter(|&b| b == b'0').count();
            let ones = s.len() - zeros;

            // 逆序遍历容量，保证每个字符串只被使用一次（0-1 背包）
            for j in (zeros..=m).rev() {
                for k in (ones..=n).rev() {
                    dp[j][k] = dp[j][k].max(dp[j - zeros][k - ones] + 1);
                }
            }
        }

        dp[m][n]
    }
}
```

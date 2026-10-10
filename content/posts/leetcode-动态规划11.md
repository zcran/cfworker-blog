---
title: "leetcode-动态规划11"
date: 2026-10-05T20:39:54+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 分割回文串 II

给你一个字符串 s，请你将 s 分割成一些子串，使每个子串都是回文串。

返回符合要求的 最少分割次数 。


```
impl Solution {
    pub fn min_cut(s: String) -> i32 {
        let bytes = s.as_bytes();
        let n = bytes.len();

        // is_pal[l][r]：子串 s[l..r] 是否为回文（左闭右开）
        // 按长度递增填充，利用 is_pal[l+1][r-1] 的结果
        let mut is_pal = vec![vec![false; n + 1]; n];
        for r in 1..=n {
            for l in (0..r).rev() {
                is_pal[l][r] = bytes[l] == bytes[r - 1]
                    && (r - l <= 2 || is_pal[l + 1][r - 1]);
            }
        }

        // dp[r]：s[..r] 分割成若干回文子串所需的最少段数
        let mut dp = vec![0i32; n + 1];
        for r in 1..=n {
            dp[r] = r as i32; // 最坏情况：每个字符单独成段
            for l in 0..r {
                if is_pal[l][r] {
                    dp[r] = dp[r].min(dp[l] + 1);
                }
            }
        }

        // 段数 - 1 即为分割次数
        dp[n] - 1
    }
}
```

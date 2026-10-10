---
title: "leetcode-动态规划27"
date: 2026-10-05T20:39:55+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 最长回文子序列

给你一个字符串 s ，找出其中最长的回文子序列，并返回该序列的长度。

子序列定义为：不改变剩余字符顺序的情况下，删除某些字符或者不删除任何字符形成的一个序列。




```
impl Solution {
    pub fn longest_palindrome_subseq(s: String) -> i32 {
        let b = s.as_bytes();
        let n = b.len();
        if n == 0 {
            return 0;
        }

        // dp[j]：当前 i 行中 s[i..=j] 的最长回文子序列长度
        // 初始等价于 i = n-1 时的状态（未显式赋值也可，因为循环里会覆盖）
        let mut dp = vec![0i32; n];

        for i in (0..n).rev() {
            dp[i] = 1;
            let mut prev = 0; // 保存 dp[i+1][j-1]，即上一轮（i+1 行）的 dp[j-1]
            for j in i + 1..n {
                let tmp = dp[j]; // 保存 dp[i+1][j]，供下一个 j 作为 dp[i+1][j-1] 使用
                dp[j] = if b[i] == b[j] {
                    prev + 2
                } else {
                    dp[j].max(dp[j - 1]) // dp[i+1][j] vs dp[i][j-1]
                };
                prev = tmp;
            }
        }

        dp[n - 1]
    }
}
```

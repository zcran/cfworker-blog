---
title: "leetcode-动态规划76"
date: 2026-10-05T20:39:59+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 让字符串成为回文串的最少插入次数

给你一个字符串 s ，每一次操作你都可以在字符串的任意位置插入任意字符。

请你返回让 s 成为回文串的 最少操作次数 。

「回文串」是正读和反读都相同的字符串。


```
impl Solution {
    /// 1312. 让字符串成为回文串的最少插入次数
    ///
    /// 最少插入次数 = n - (s 与 s 反转的最长公共子序列长度)。
    /// 因为保留的 LCS 可以左右配对成为回文骨架，其余字符各需一次插入。
    ///
    /// 这里用一维滚动数组求 s 与自身反转的 LCS：
    ///   dp[j] 表示当前字符与反转串前 j 个字符的 LCS 长度。
    ///   pre 保存上一行对角线（dp[j-1] 的旧值），tmp 暂存当前 dp[j]。
    pub fn min_insertions(s: String) -> i32 {
        let n = s.len();
        let bytes = s.as_bytes();
        let mut dp = vec![0i32; n + 1];

        for i in 1..=n {
            let mut pre = 0; // 上一行的 dp[j-1]（对角线）
            for j in 1..=n {
                let tmp = dp[j]; // 保存旧值，供下一轮当对角线用
                dp[j] = if bytes[i - 1] == bytes[n - j] {
                    pre + 1
                } else {
                    dp[j].max(dp[j - 1])
                };
                pre = tmp;
            }
        }

        (n as i32) - dp[n]
    }
}
```

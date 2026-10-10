---
title: "leetcode-动态规划67"
date: 2026-10-05T20:39:58+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 最长公共子序列

给定两个字符串 text1 和 text2，返回这两个字符串的最长 公共子序列 的长度。如果不存在 公共子序列 ，返回 0 。

一个字符串的 子序列 是指这样一个新的字符串：它是由原字符串在不改变字符的相对顺序的情况下删除某些字符（也可以不删除任何字符）后组成的新字符串。

例如，"ace" 是 "abcde" 的子序列，但 "aec" 不是 "abcde" 的子序列。
两个字符串的 公共子序列 是这两个字符串所共同拥有的子序列。


```
impl Solution {
    /// 1143. 最长公共子序列
    ///
    /// dp[j]：处理到 text1 当前字符时，与 text2 前 j 个字符的 LCS 长度。
    /// 对 text1 的每个字符 c，倒序遍历 j：
    ///   - 若 c == text2[j-1]：dp[j] = 上一行对角线值 + 1
    ///   - 否则：dp[j] = max(dp[j], dp[j-1])（分别对应"不用 c"和"不用 text2[j-1]"）
    ///
    /// 用一维滚动数组，把空间从 O(m*n) 降到 O(n)；
    /// 用 prev 保存上一行对角线值，避免覆盖前丢失。
    pub fn longest_common_subsequence(text1: String, text2: String) -> i32 {
        let (s1, s2) = (text1.as_bytes(), text2.as_bytes());
        let n = s2.len();
        let mut dp = vec![0i32; n + 1];

        for &c in s1 {
            let mut prev = 0; // 上一行 dp[j-1]（对角线）的值
            for j in 1..=n {
                let temp = dp[j]; // 保存当前 dp[j]，作为下一轮的"上一行对角线"
                dp[j] = if c == s2[j - 1] {
                    prev + 1
                } else {
                    dp[j].max(dp[j - 1])
                };
                prev = temp;
            }
        }

        dp[n]
    }
}
```

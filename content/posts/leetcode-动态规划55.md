---
title: "leetcode-动态规划55"
date: 2026-10-05T20:39:57+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 不同的子序列 II

给定一个字符串 s，计算 s 的 不同非空子序列 的个数。因为结果可能很大，所以返回答案需要对 10^9 + 7 取余 。

字符串的 子序列 是经由原字符串删除一些（也可能不删除）字符但不改变剩余字符相对位置的一个新字符串。

例如，"ace" 是 "abcde" 的一个子序列，但 "aec" 不是。


```
impl Solution {
    /// 940. 不同的子序列 II
    ///
    /// dp[c] 表示以字符 c 结尾的不同非空子序列个数。
    /// 遍历 s 中每个字符 c 时，新的以 c 结尾的子序列 = 之前所有字符结尾的子序列之和 + 1（单独的 c 本身）。
    /// 由于"以 c 结尾"的结果会被整体覆盖，天然去重。
    ///
    /// 维护 total = Σ dp[c]，则每个字符的处理为：
    ///   new = total + 1
    ///   total = total - dp[c] + new = 2 * total + 1 - dp[c]
    ///   dp[c] = new
    ///
    /// 用变量 total 代替每次求和，时间 O(n)，空间 O(26)。
    pub fn distinct_subseq_ii(s: String) -> i32 {
        const MOD: i64 = 1_000_000_007;
        let mut dp = [0i64; 26];
        let mut total = 0i64;

        for b in s.bytes() {
            let c = (b - b'a') as usize;
            let new = (total + 1) % MOD;
            total = (total - dp[c] + new + MOD) % MOD;
            dp[c] = new;
        }

        total as i32
    }
}
```

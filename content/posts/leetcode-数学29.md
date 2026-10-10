---
title: "leetcode-数学29"
date: 2026-10-07T20:39:30+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 仅含 1 的子串数

给你一个二进制字符串 s（仅由 '0' 和 '1' 组成的字符串）。

返回所有字符都为 1 的子字符串的数目。

由于答案可能很大，请你将它对 10^9 + 7 取模后返回。


```
impl Solution {
    /// 统计二进制字符串 s 中所有字符均为 '1' 的子串数目，并对 1e9+7 取模。
    ///
    /// 一段连续的 '1' 长度为 L 时，可形成的全 '1' 子串数为 L*(L+1)/2。
    /// 遍历字符串，遇 '1' 累加长度，遇 '0' 结算当前段并清零，最后再结算一次。
    pub fn num_sub(s: String) -> i32 {
        const MOD: i64 = 1_000_000_007;

        let mut ans: i64 = 0;
        let mut run: i64 = 0; // 当前连续 '1' 的长度

        for b in s.bytes() {
            if b == b'1' {
                run += 1;
            } else {
                // 结算上一段连续 '1'
                ans = (ans + run * (run + 1) / 2) % MOD;
                run = 0;
            }
        }
        // 结算末尾的连续 '1'
        ans = (ans + run * (run + 1) / 2) % MOD;

        ans as i32
    }
}
```

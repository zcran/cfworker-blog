---
title: "leetcode-动态规划5"
date: 2026-10-05T20:39:54+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 不同的子序列

给你两个字符串 s 和 t ，统计并返回在 s 的 子序列 中 t 出现的个数。

测试用例保证结果在 32 位有符号整数范围内。


```
impl Solution {
    pub fn num_distinct(s: String, t: String) -> i32 {
        let t = t.as_bytes();
        let m = t.len();

        // dp[j]：t 的前 j 个字符在 s 已处理前缀中出现的子序列个数
        let mut dp = vec![0i32; m + 1];
        dp[0] = 1; // 空串是任意字符串的子序列

        for &c in s.as_bytes() {
            // 逆序更新，保证每个字符只被使用一次
            for j in (1..=m).rev() {
                if t[j - 1] == c {
                    dp[j] += dp[j - 1];
                }
            }
        }

        dp[m]
    }
}
```

---
title: "leetcode-动态规划39"
date: 2026-10-05T20:39:56+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 两个字符串的最小ASCII删除和

给定两个字符串s1 和 s2，返回 使两个字符串相等所需删除字符的 ASCII 值的最小和 。


```
impl Solution {
    pub fn minimum_delete_sum(s1: String, s2: String) -> i32 {
        let (a, b) = (s1.as_bytes(), s2.as_bytes());
        let (m, n) = (a.len(), b.len());

        // dp[j]：处理到当前字符时，s1 前缀与 s2 前 j 个字符相等所需删除的最小 ASCII 和
        let mut dp: Vec<i32> = (0..=n)
            .scan(0, |acc, j| {
                if j > 0 {
                    *acc += b[j - 1] as i32;
                }
                Some(*acc)
            })
            .collect();

        for i in 1..=m {
            let mut prev = dp[0]; // 保存 dp[i-1][j-1]
            dp[0] += a[i - 1] as i32; // dp[i][0]

            for j in 1..=n {
                let tmp = dp[j];
                dp[j] = if a[i - 1] == b[j - 1] {
                    prev // 字符相同，保留
                } else {
                    // 删除 s1[i-1] 或 s2[j-1]，取较小代价
                    (dp[j] + a[i - 1] as i32).min(dp[j - 1] + b[j - 1] as i32)
                };
                prev = tmp;
            }
        }

        dp[n]
    }
}
```

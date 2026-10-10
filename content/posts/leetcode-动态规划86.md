---
title: "leetcode-动态规划86"
date: 2026-10-05T20:39:59+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 分割回文串 IV

给你一个字符串 s ，如果可以将它分割成三个 非空 回文子字符串，那么返回 true ，否则返回 false 。

当一个字符串正着读和反着读是一模一样的，就称其为 回文字符串 。


```
impl Solution {
    pub fn check_partitioning(s: String) -> bool {
        let s = s.as_bytes();
        let n = s.len();

        // pal[l][r]: s[l..=r] 是否为回文
        // 用一维 DP 预处理所有回文子串
        let mut pal = vec![vec![false; n]; n];
        for i in (0..n).rev() {
            for j in i..n {
                if s[i] == s[j] && (j - i < 2 || pal[i + 1][j - 1]) {
                    pal[i][j] = true;
                }
            }
        }

        // 枚举第一刀和第二刀的位置
        // 第一段 s[0..i)，第二段 s[i..j)，第三段 s[j..n)
        for i in 1..n - 1 {
            if !pal[0][i - 1] {
                continue;
            }
            for j in i + 1..n {
                if pal[i][j - 1] && pal[j][n - 1] {
                    return true;
                }
            }
        }

        false
    }
}
```

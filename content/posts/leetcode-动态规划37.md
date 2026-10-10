---
title: "leetcode-动态规划37"
date: 2026-10-05T20:39:56+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 奇怪的打印机

有台奇怪的打印机有以下两个特殊要求：

打印机每次只能打印由 同一个字符 组成的序列。

每次可以在从起始到结束的任意位置打印新字符，并且会覆盖掉原来已有的字符。

给你一个字符串 s ，你的任务是计算这个打印机打印它需要的最少打印次数。


```
impl Solution {
    pub fn strange_printer(s: String) -> i32 {
        let s = s.as_bytes();
        let n = s.len();

        // dp[i][j]：打印 s[i..=j] 所需的最少次数
        let mut dp = vec![vec![0i32; n]; n];

        // 从右往左、区间长度递增地递推
        for i in (0..n).rev() {
            dp[i][i] = 1;
            for j in i + 1..n {
                dp[i][j] = if s[i] == s[j] {
                    // 端点字符相同，可在打印 s[i] 时顺带覆盖 s[j]
                    dp[i][j - 1]
                } else {
                    // 枚举分割点 k，合并两段打印
                    (i..j).map(|k| dp[i][k] + dp[k + 1][j]).min().unwrap()
                };
            }
        }

        if n == 0 { 0 } else { dp[0][n - 1] }
    }
}
```

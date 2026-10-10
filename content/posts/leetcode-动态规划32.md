---
title: "leetcode-动态规划32"
date: 2026-10-05T20:39:56+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 两个字符串的删除操作

给定两个单词 word1 和 word2 ，返回使得 word1 和  word2 相同所需的最小步数。

每步 可以删除任意一个字符串中的一个字符。


```
impl Solution {
    pub fn min_distance(word1: String, word2: String) -> i32 {
        let (a, b) = (word1.as_bytes(), word2.as_bytes());

        // 用较短的一侧作为 DP 维度，减小内存
        let (a, b) = if a.len() >= b.len() { (a, b) } else { (b, a) };
        let n = b.len();

        // dp[j]：a 的前 i 个字符与 b 的前 j 个字符相同所需的最小删除步数
        let mut dp: Vec<i32> = (0..=n as i32).collect();

        for (i, &ca) in a.iter().enumerate() {
            let mut prev = dp[0]; // 保存 dp[i-1][j-1]
            dp[0] = i as i32 + 1;

            for (j, &cb) in b.iter().enumerate() {
                let tmp = dp[j + 1]; // 保存 dp[i-1][j]
                dp[j + 1] = if ca == cb {
                    prev // 字符相同，无需删除
                } else {
                    // 删除 a[i] 或 b[j]，或两者都删（等价于 prev + 2）
                    (dp[j + 1] + 1).min(dp[j] + 1).min(prev + 2)
                };
                prev = tmp;
            }
        }

        dp[n]
    }
}
```

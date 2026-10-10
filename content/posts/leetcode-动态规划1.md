---
title: "leetcode-动态规划1"
date: 2026-10-05T20:39:53+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 编辑距离

给你两个单词 word1 和 word2， 请返回将 word1 转换成 word2 所使用的最少操作数  。

你可以对一个单词进行如下三种操作：

插入一个字符
删除一个字符
替换一个字符


```
impl Solution {
    pub fn min_distance(word1: String, word2: String) -> i32 {
        let (a, b) = (word1.as_bytes(), word2.as_bytes());

        // 令 b 为较短的一侧，减小 DP 数组长度
        let (a, b) = if a.len() >= b.len() { (a, b) } else { (b, a) };
        let n = b.len();

        // dp[j]：处理到当前字符时，a 的前缀与 b 的前 j 个字符的编辑距离
        let mut dp: Vec<usize> = (0..=n).collect();

        for (i, &ca) in a.iter().enumerate() {
            let mut prev = dp[0]; // 对应 dp[i][j-1] 的旧值（左上角）
            dp[0] = i + 1;

            for (j, &cb) in b.iter().enumerate() {
                let temp = dp[j + 1];
                dp[j + 1] = if ca == cb {
                    prev
                } else {
                    prev.min(dp[j]).min(temp) + 1
                };
                prev = temp;
            }
        }

        dp[n] as i32
    }
}
```

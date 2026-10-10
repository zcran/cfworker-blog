---
title: "leetcode-动态规划89"
date: 2026-10-05T20:40:00+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 由子序列构造的最长回文串的长度

给你两个字符串 word1 和 word2 ，请你按下述方法构造一个字符串：

从 word1 中选出某个 非空 子序列 subsequence1 。
从 word2 中选出某个 非空 子序列 subsequence2 。
连接两个子序列 subsequence1 + subsequence2 ，得到字符串。

返回可按上述方法构造的最长 回文串 的 长度 。如果无法构造回文串，返回 0 。

字符串 s 的一个 子序列 是通过从 s 中删除一些（也可能不删除）字符而不更改其余字符的顺序生成的字符串。

回文串 是正着读和反着读结果一致的字符串。


```
impl Solution {
    pub fn longest_palindrome(word1: String, word2: String) -> i32 {
        let w1 = word1.len();
        let s: Vec<u8> = word1.bytes().chain(word2.bytes()).collect();
        let n = s.len();

        // f[i][j]: s[i..=j] 中最长回文子序列的长度
        let mut f = vec![vec![0usize; n]; n];
        let mut ans = 0;

        for i in (0..n).rev() {
            f[i][i] = 1;
            for j in i + 1..n {
                if s[i] == s[j] {
                    // 两端字符相同，可同时纳入回文
                    f[i][j] = f[i + 1][j - 1] + 2;
                    // 只有当 i 属于 word1、j 属于 word2 时，才满足跨串选择的要求
                    if i < w1 && j >= w1 {
                        ans = ans.max(f[i][j]);
                    }
                } else {
                    // 否则舍弃一端，取较优者
                    f[i][j] = f[i + 1][j].max(f[i][j - 1]);
                }
            }
        }

        ans as i32
    }
}
```

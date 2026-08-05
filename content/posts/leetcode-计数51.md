---
title: "leetcode-计数51"
date: 2026-08-01T10:40:53+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 检查两个字符串是否几乎相等

如果两个字符串 word1 和 word2 中从 'a' 到 'z' 每一个字母出现频率之差都 不超过 3 ，那么我们称这两个字符串 word1 和 word2 几乎相等 。

给你两个长度都为 n 的字符串 word1 和 word2 ，如果 word1 和 word2 几乎相等 ，请你返回 true ，否则返回 false 。

一个字母 x 的出现 频率 指的是它在字符串中出现的次数。


```
impl Solution {
    pub fn check_almost_equivalent(word1: String, word2: String) -> bool {
        // 用一个数组记录每个字母的净频率（word1 - word2）
        let mut diff = [0i32; 26];

        // word1 中的字母增加计数
        for b in word1.bytes() {
            diff[(b - b'a') as usize] += 1;
        }

        // word2 中的字母减少计数
        for b in word2.bytes() {
            diff[(b - b'a') as usize] -= 1;
        }

        // 检查所有字母的频率差绝对值是否都不超过 3
        diff.iter().all(|&count| count.abs() <= 3)
    }
}
```

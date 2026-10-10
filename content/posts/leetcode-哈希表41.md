---
title: "leetcode-哈希表41"
date: 2026-10-09T20:07:58+08:00
tags: ["leetcode", "哈希表"]
draft: false
---


## 两个相同字符之间的最长子字符串

给你一个字符串 s，请你返回 两个相同字符之间的最长子字符串的长度 ，计算长度时不含这两个字符。如果不存在这样的子字符串，返回 -1 。

子字符串 是字符串中的一个连续字符序列。


```
impl Solution {
    pub fn max_length_between_equal_characters(s: String) -> i32 {
        // 对每个字母只记录它第一次出现的下标（用 0 表示未出现，故存 i+1）。
        // 之后每次遇到相同字母，用当前下标减去首次下标再减 1，
        // 即为两字符之间的长度，取最大值。
        let mut first = [0i32; 26];
        let mut ans = -1;

        for (i, b) in s.bytes().enumerate() {
            let idx = (b - b'a') as usize;
            let i = i as i32;
            if first[idx] == 0 {
                first[idx] = i + 1;
            } else {
                ans = ans.max(i - first[idx]);
            }
        }

        ans
    }
}
```

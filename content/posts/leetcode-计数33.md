---
title: "leetcode-计数33"
date: 2026-08-01T10:40:52+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 统计一致字符串的数目

给你一个由不同字符组成的字符串 allowed 和一个字符串数组 words 。如果一个字符串的每一个字符都在 allowed 中，就称这个字符串是 一致字符串 。

请你返回 words 数组中 一致字符串 的数目。


```
impl Solution {
    pub fn count_consistent_strings(allowed: String, words: Vec<String>) -> i32 {
        // 构建允许字符的位掩码
        let mut mask = 0u32;
        for &b in allowed.as_bytes() {
            mask |= 1 << (b - b'a');
        }

        // 统计一致字符串数量
        words.iter()
            .filter(|word| {
                word.bytes().all(|b| mask & (1 << (b - b'a')) != 0)
            })
            .count() as i32
    }
}
```

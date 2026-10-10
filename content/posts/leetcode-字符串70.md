---
title: "leetcode-字符串70"
date: 2026-10-10T12:56:57+08:00
tags: ["leetcode", "字符串"]
draft: false
---


## 字符串的分数

给你一个字符串 s 。一个字符串的 分数 定义为相邻字符 ASCII 码差值绝对值的和。

请你返回 s 的 分数 。


```
impl Solution {
    pub fn score_of_string(s: String) -> i32 {
        s.as_bytes()
            .windows(2)
            .map(|w| (w[0] as i32 - w[1] as i32).abs())
            .sum()
    }
}
```

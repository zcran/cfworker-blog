---
title: "leetcode-字符串49"
date: 2026-10-10T12:56:55+08:00
tags: ["leetcode", "字符串"]
draft: false
---


## 统计是给定字符串前缀的字符串数目

给你一个字符串数组 words 和一个字符串 s ，其中 words[i] 和 s 只包含 小写英文字母 。

请你返回 words 中是字符串 s 前缀 的 字符串数目 。

一个字符串的 前缀 是出现在字符串开头的子字符串。子字符串 是一个字符串中的连续一段字符序列。




```
impl Solution {
    pub fn count_prefixes(words: Vec<String>, s: String) -> i32 {
        words.iter().filter(|w| s.starts_with(w.as_str())).count() as i32
    }
}
```

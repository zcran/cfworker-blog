---
title: "leetcode-字符串64"
date: 2026-10-10T12:56:57+08:00
tags: ["leetcode", "字符串"]
draft: false
---


## 判别首字母缩略词

给你一个字符串数组 words 和一个字符串 s ，请你判断 s 是不是 words 的 首字母缩略词 。

如果可以按顺序串联 words 中每个字符串的第一个字符形成字符串 s ，则认为 s 是 words 的首字母缩略词。例如，"ab" 可以由 ["apple", "banana"] 形成，但是无法从 ["bear", "aardvark"] 形成。

如果 s 是 words 的首字母缩略词，返回 true ；否则，返回 false 。


```
impl Solution {
    pub fn is_acronym(words: Vec<String>, s: String) -> bool {
        // 长度先不等则直接失败，避免后续构造
        if words.len() != s.len() {
            return false;
        }
        words
            .iter()
            .zip(s.bytes())
            .all(|(w, c)| w.as_bytes()[0] == c)
    }
}
```

---
title: "leetcode-双指针84"
date: 2026-09-05T09:53:06+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 找出数组中的第一个回文字符串

给你一个字符串数组 words ，找出并返回数组中的 第一个回文字符串 。如果不存在满足要求的字符串，返回一个 空字符串 "" 。

回文字符串 的定义为：如果一个字符串正着读和反着读一样，那么该字符串就是一个 回文字符串 。


```
impl Solution {
    pub fn first_palindrome(words: Vec<String>) -> String {
        // 找到第一个回文串，否则返回空字符串
        words
            .into_iter()
            .find(|s| s.as_bytes().iter().eq(s.as_bytes().iter().rev()))
            .unwrap_or_default()
    }
}
```

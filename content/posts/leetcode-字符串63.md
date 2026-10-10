---
title: "leetcode-字符串63"
date: 2026-10-10T12:56:57+08:00
tags: ["leetcode", "字符串"]
draft: false
---


## 按分隔符拆分字符串

给你一个字符串数组 words 和一个字符 separator ，请你按 separator 拆分 words 中的每个字符串。

返回一个由拆分后的新字符串组成的字符串数组，不包括空字符串 。

注意

separator 用于决定拆分发生的位置，但它不包含在结果字符串中。
拆分可能形成两个以上的字符串。
结果字符串必须保持初始相同的先后顺序。


```
impl Solution {
    pub fn split_words_by_separator(words: Vec<String>, separator: char) -> Vec<String> {
        words
            .iter()
            .flat_map(|s| s.split(separator))
            .filter(|s| !s.is_empty())
            .map(String::from)
            .collect()
    }
}
```

---
title: "leetcode-字符串68"
date: 2026-10-10T12:56:57+08:00
tags: ["leetcode", "字符串"]
draft: false
---


## 查找包含给定字符的单词

给你一个下标从 0 开始的字符串数组 words 和一个字符 x 。

请你返回一个 下标数组 ，表示下标在数组中对应的单词包含字符 x 。

注意 ，返回的数组可以是 任意 顺序。


```
impl Solution {
    pub fn find_words_containing(words: Vec<String>, x: char) -> Vec<i32> {
        words
            .iter()
            .enumerate()
            .filter(|(_, w)| w.contains(x))
            .map(|(i, _)| i as i32)
            .collect()
    }
}
```

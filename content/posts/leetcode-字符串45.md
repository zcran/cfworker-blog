---
title: "leetcode-字符串45"
date: 2026-10-10T12:56:55+08:00
tags: ["leetcode", "字符串"]
draft: false
---


## 句子中的最多单词数

一个 句子 由一些 单词 以及它们之间的单个空格组成，句子的开头和结尾不会有多余空格。

给你一个字符串数组 sentences ，其中 sentences[i] 表示单个 句子 。

请你返回单个句子里 单词的最多数目 。


```
impl Solution {
    pub fn most_words_found(sentences: Vec<String>) -> i32 {
        // 空格数 + 1 即为单词数，避免 split 分配
        sentences
            .iter()
            .map(|s| s.bytes().filter(|&b| b == b' ').count() as i32 + 1)
            .max()
            .unwrap()
    }
}
```

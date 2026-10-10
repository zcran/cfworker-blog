---
title: "leetcode-字符串19"
date: 2026-10-10T12:56:52+08:00
tags: ["leetcode", "字符串"]
draft: false
---


## Bigram 分词

给出第一个词 first 和第二个词 second，考虑在某些文本 text 中可能以 "first second third" 形式出现的情况，其中 second 紧随 first 出现，third 紧随 second 出现。

对于每种这样的情况，将第三个词 "third" 添加到答案中，并返回答案。


```
impl Solution {
    pub fn find_ocurrences(text: String, first: String, second: String) -> Vec<String> {
        // 用 three 个词的滑动窗口遍历文本
        let words: Vec<&str> = text.split_whitespace().collect();
        words
            .windows(3)
            .filter(|w| w[0] == first && w[1] == second)
            .map(|w| w[2].to_string())
            .collect()
    }
}
```

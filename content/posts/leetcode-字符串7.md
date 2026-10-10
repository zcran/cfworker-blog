---
title: "leetcode-字符串7"
date: 2026-10-10T12:56:51+08:00
tags: ["leetcode", "字符串"]
draft: false
---


## 字符串中的单词数

统计字符串中的单词个数，这里的单词指的是连续的不是空格的字符。

请注意，你可以假定字符串里不包括任何不可打印的字符。


```
impl Solution {
    pub fn count_segments(s: String) -> i32 {
        // 单词 = 非空格字符段，统计每个「非空格且前一个为空格或开头」的位置
        s.bytes()
            .enumerate()
            .filter(|&(i, b)| b != b' ' && (i == 0 || s.as_bytes()[i - 1] == b' '))
            .count() as i32
    }
}
```

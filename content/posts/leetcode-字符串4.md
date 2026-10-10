---
title: "leetcode-字符串4"
date: 2026-10-10T12:56:51+08:00
tags: ["leetcode", "字符串"]
draft: false
---


## 最后一个单词的长度

给你一个字符串 s，由若干单词组成，单词前后用一些空格字符隔开。返回字符串中 最后一个 单词的长度。

单词 是指仅由字母组成、不包含任何空格字符的最大子字符串。


```
impl Solution {
    pub fn length_of_last_word(s: String) -> i32 {
        // 从右往左跳过尾部空格，再统计最后一个单词的长度
        s.bytes()
            .rev()
            .skip_while(|&b| b == b' ')
            .take_while(|&b| b != b' ')
            .count() as i32
    }
}
```

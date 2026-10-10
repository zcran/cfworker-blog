---
title: "leetcode-哈希表50"
date: 2026-10-09T20:07:58+08:00
tags: ["leetcode", "哈希表"]
draft: false
---


## 可以输入的最大单词数

键盘出现了一些故障，有些字母键无法正常工作。而键盘上所有其他键都能够正常工作。

给你一个由若干单词组成的字符串 text ，单词间由单个空格组成（不含前导和尾随空格）；另有一个字符串 brokenLetters ，由所有已损坏的不同字母键组成，返回你可以使用此键盘完全输入的 text 中单词的数目。


```
use std::collections::HashSet;

impl Solution {
    pub fn can_be_typed_words(text: String, broken_letters: String) -> i32 {
        // 用 26 位掩码标记损坏字母，避免 HashSet 开销。
        let broken: u32 = broken_letters.bytes().fold(0, |m, b| m | (1 << (b - b'a')));

        // 按空格切分单词，每个单词的字母都不在损坏集合中才计数。
        text.split(' ')
            .filter(|word| word.bytes().all(|b| broken & (1 << (b - b'a')) == 0))
            .count() as i32
    }
}
```

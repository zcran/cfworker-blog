---
title: "leetcode-双指针36"
date: 2026-09-05T09:53:03+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 反转字符串中的单词 III

给定一个字符串 s ，你需要反转字符串中每个单词的字符顺序，同时仍保留空格和单词的初始顺序。


```
impl Solution {
    pub fn reverse_words(s: String) -> String {
        let mut bytes = s.into_bytes();
        let mut start = 0;

        // 遍历字节，找到每个单词的边界
        for i in 0..=bytes.len() {
            if i == bytes.len() || bytes[i] == b' ' {
                // 反转当前单词 [start, i)
                bytes[start..i].reverse();
                start = i + 1;
            }
        }

        String::from_utf8(bytes).unwrap()
    }
}
```

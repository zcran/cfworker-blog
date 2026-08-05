---
title: "leetcode-计数17"
date: 2026-08-01T10:40:51+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 拼写单词

给定一个字符串数组 words 和一个字符串 chars。

如果字符串可以由 chars 中的字符组成（每个字符在 每个 words 中只能使用一次），则认为它是好的。

返回 words 中所有好的字符串的长度之和。


```
impl Solution {
    pub fn count_characters(words: Vec<String>, chars: String) -> i32 {
        // 统计可用字符（chars）中每个字母的出现次数
        let mut available = [0; 26];
        for ch in chars.bytes() {
            available[(ch - b'a') as usize] += 1;
        }

        let mut total_length = 0;

        // 检查每个单词
        'word_loop: for word in &words {
            // 统计当前单词中每个字母的出现次数
            let mut word_count = [0; 26];
            for ch in word.bytes() {
                let idx = (ch - b'a') as usize;
                word_count[idx] += 1;

                // 提前终止：如果某个字母超过可用数量，直接跳过该单词
                if word_count[idx] > available[idx] {
                    continue 'word_loop;
                }
            }

            // 所有字母都满足要求，累加单词长度
            total_length += word.len() as i32;
        }

        total_length
    }
}
```

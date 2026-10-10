---
title: "leetcode-哈希表73"
date: 2026-10-09T20:08:00+08:00
tags: ["leetcode", "哈希表"]
draft: false
---


## 举报垃圾信息

给你一个字符串数组 message 和一个字符串数组 bannedWords。

如果数组中 至少 存在两个单词与 bannedWords 中的任一单词 完全相同，则该数组被视为 垃圾信息。

如果数组 message 是垃圾信息，则返回 true；否则返回 false。




```
use std::collections::HashSet;

impl Solution {
    pub fn report_spam(message: Vec<String>, banned_words: Vec<String>) -> bool {
        // 用集合加速判断某个单词是否属于禁用词
        let banned: HashSet<&str> = banned_words.iter().map(String::as_str).collect();

        // 统计 message 中属于禁用词的单词个数，达到 2 即可判定为垃圾信息
        let mut cnt = 0;
        for word in &message {
            if banned.contains(word.as_str()) {
                cnt += 1;
                if cnt > 1 {
                    return true;
                }
            }
        }

        false
    }
}
```

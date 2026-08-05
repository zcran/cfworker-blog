---
title: "leetcode-计数9"
date: 2026-08-01T10:40:51+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 最常见的单词

给你一个字符串 paragraph 和一个表示禁用词的字符串数组 banned ，返回出现频率最高的非禁用词。题目数据 保证 至少存在一个非禁用词，且答案 唯一 。

paragraph 中的单词 不区分大小写 ，答案应以 小写 形式返回。

注意 单词不包含标点符号。


```
impl Solution {
    pub fn most_common_word(paragraph: String, banned: Vec<String>) -> String {
        use std::collections::{HashMap, HashSet};

        // 将禁用词存入 HashSet 以便快速查找
        let banned_set: HashSet<&str> = banned.iter().map(|s| s.as_str()).collect();

        let mut freq = HashMap::new();
        let mut max_count = 0;
        let mut result = String::new();

        // 按标点和空白分割单词
        for word in paragraph.split(|c: char| c.is_ascii_punctuation() || c.is_ascii_whitespace()) {
            if word.is_empty() {
                continue;
            }

            let word_lower = word.to_ascii_lowercase();

            // 跳过禁用词
            if banned_set.contains(word_lower.as_str()) {
                continue;
            }

            // 统计词频（克隆一份用于后续可能的结果赋值）
            let count = freq.entry(word_lower.clone()).or_insert(0);
            *count += 1;

            // 跟踪当前最高频词
            if *count > max_count {
                max_count = *count;
                result = word_lower; // 移动所有权，无需额外查找
            }
        }

        result
    }
}
```

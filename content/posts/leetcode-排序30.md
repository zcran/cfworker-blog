---
title: "leetcode-排序30"
date: 2026-10-05T00:35:01+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 重新排列句子中的单词

「句子」是一个用空格分隔单词的字符串。给你一个满足下述格式的句子 text :

句子的首字母大写
text 中的每个单词都用单个空格分隔。

请你重新排列 text 中的单词，使所有单词按其长度的升序排列。如果两个单词的长度相同，则保留其在原句子中的相对顺序。

请同样按上述格式返回新的句子。




```
impl Solution {
    /// 将句子中的单词按长度升序重排，长度相同保持原相对顺序
    ///
    /// 思路：首字母先转小写，用稳定排序按单词长度排列（保证同长度相对顺序不变），
    /// 用空格连接后再把首字母还原为大写。
    /// 时间 O(n log n)，额外空间 O(n)。
    pub fn arrange_words(text: String) -> String {
        // 首字母转小写，避免大写影响后续比较（此处只涉及首字母的还原）
        let mut bytes = text.into_bytes();
        bytes[0] = bytes[0].to_ascii_lowercase();
        let lower = unsafe { String::from_utf8_unchecked(bytes) };

        // 稳定排序：sort_by_key 是稳定的，同长度单词保持原顺序
        let mut words: Vec<&str> = lower.split_ascii_whitespace().collect();
        words.sort_by_key(|w| w.len());

        // 连接后还原首字母为大写
        let mut result = words.join(" ").into_bytes();
        result[0] = result[0].to_ascii_uppercase();
        unsafe { String::from_utf8_unchecked(result) }
    }
}
```

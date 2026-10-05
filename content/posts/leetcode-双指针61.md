---
title: "leetcode-双指针61"
date: 2026-09-05T09:53:04+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 最长字符串链

给出一个单词数组 words ，其中每个单词都由小写英文字母组成。

如果我们可以 不改变其他字符的顺序 ，在 wordA 的任何地方添加 恰好一个 字母使其变成 wordB ，那么我们认为 wordA 是 wordB 的 前身 。

例如，"abc" 是 "abac" 的 前身 ，而 "cba" 不是 "bcad" 的 前身

词链是单词 [word_1, word_2, ..., word_k] 组成的序列，k >= 1，其中 word1 是 word2 的前身，word2 是 word3 的前身，依此类推。一个单词通常是 k == 1 的 单词链 。

从给定单词列表 words 中选择单词组成词链，返回 词链的 最长可能长度 。


```
impl Solution {
    pub fn longest_str_chain(mut words: Vec<String>) -> i32 {
        use std::collections::HashMap;

        // 按长度升序排序，确保处理前身时其 dp 值已计算
        words.sort_by_key(|s| s.len());

        let mut dp = HashMap::with_capacity(words.len());
        let mut max_chain = 0;

        for word in words {
            let bytes = word.as_bytes();
            let mut best = 1; // 至少自身构成链

            // 尝试删除每个位置的一个字符，构造所有可能的前身
            for i in 0..bytes.len() {
                // 使用字节数组拼接，避免多次 String 分配
                let mut prev = Vec::with_capacity(bytes.len() - 1);
                prev.extend_from_slice(&bytes[..i]);
                prev.extend_from_slice(&bytes[i + 1..]);

                // 将前身转为 String 用于查找（但使用 from_utf8_unchecked 节省检查）
                let prev_str = unsafe { String::from_utf8_unchecked(prev) };
                if let Some(&len) = dp.get(&prev_str) {
                    best = best.max(len + 1);
                }
            }

            dp.insert(word, best);
            max_chain = max_chain.max(best);
        }

        max_chain
    }
}
```

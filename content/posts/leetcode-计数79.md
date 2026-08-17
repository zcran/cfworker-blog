---
title: "leetcode-计数79"
date: 2026-08-01T10:40:55+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 统计相似字符串对的数目

给你一个下标从 0 开始的字符串数组 words 。

如果两个字符串由相同的字符组成，则认为这两个字符串 相似 。

例如，"abca" 和 "cba" 相似，因为它们都由字符 'a'、'b'、'c' 组成。

然而，"abacba" 和 "bcfd" 不相似，因为它们不是相同字符组成的。

请你找出满足字符串 words[i] 和 words[j] 相似的下标对 (i, j) ，并返回下标对的数目，其中 0 <= i < j <= words.length - 1 。




```
use std::collections::HashMap;

impl Solution {
    pub fn similar_pairs(words: Vec<String>) -> i32 {
        // 用位掩码表示每个字符串包含的字符集合（26位）
        let mut freq = HashMap::with_capacity(words.len());
        let mut pairs = 0;

        for word in words {
            let mut mask = 0u32;
            for &b in word.as_bytes() {
                mask |= 1 << (b - b'a'); // 设置对应位
            }

            // 当前字符串与之前所有相同掩码的字符串都相似
            let count = freq.get(&mask).copied().unwrap_or(0);
            pairs += count;
            freq.insert(mask, count + 1);
        }

        pairs
    }
}
```

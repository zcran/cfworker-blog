---
title: "leetcode-计数54"
date: 2026-08-01T10:40:53+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 连接两字母单词得到的最长回文串

给你一个字符串数组 words 。words 中每个元素都是一个包含 两个 小写英文字母的单词。

请你从 words 中选择一些元素并按 任意顺序 连接它们，并得到一个 尽可能长的回文串 。每个元素 至多 只能使用一次。

请你返回你能得到的最长回文串的 长度 。如果没办法得到任何一个回文串，请你返回 0 。

回文串 指的是从前往后和从后往前读一样的字符串。


```
use std::collections::HashMap;

impl Solution {
    pub fn longest_palindrome(words: Vec<String>) -> i32 {
        // 统计每个单词出现次数
        let mut freq = HashMap::with_capacity(words.len());
        for word in words {
            *freq.entry(word).or_insert(0) += 1;
        }

        let mut len = 0;
        let mut has_center = false;

        for (word, &count) in &freq {
            let bytes = word.as_bytes();
            // 构造反转字符串：交换两个字符
            let rev = String::from_utf8_lossy(&[bytes[1], bytes[0]]).into_owned();

            if word == &rev {
                // "aa", "bb" 等对称词：成对使用，奇数个可留一个做中心
                len += (count / 2) * 4;
                if count % 2 == 1 {
                    has_center = true;
                }
            } else if word < &rev {
                // 只处理字典序小的，配对大的，避免重复
                let rev_count = freq.get(&rev).unwrap_or(&0);
                len += 4 * count.min(*rev_count);
            }
        }

        // 中心可以放一个对称词
        if has_center {
            len += 2;
        }

        len
    }
}
```

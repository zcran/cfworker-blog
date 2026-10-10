---
title: "leetcode-哈希表46"
date: 2026-10-09T20:07:58+08:00
tags: ["leetcode", "哈希表"]
draft: false
---


## 字符串中不同整数的数目

给你一个字符串 word ，该字符串由数字和小写英文字母组成。

请你用空格替换每个不是数字的字符。例如，"a123bc34d8ef34" 将会变成 " 123  34 8  34" 。注意，剩下的这些整数为（相邻彼此至少有一个空格隔开）："123"、"34"、"8" 和 "34" 。

返回对 word 完成替换后形成的 不同 整数的数目。

只有当两个整数的 不含前导零 的十进制表示不同， 才认为这两个整数也不同。


```
use std::collections::HashSet;

impl Solution {
    pub fn num_different_integers(word: String) -> i32 {
        // 按非数字字符切分，得到所有数字子串；
        // 去掉前导零后放入集合去重（"0" 会变成空串，需统一为 "0"）。
        word.split(|c: char| !c.is_ascii_digit())
            .filter(|s| !s.is_empty())
            .map(|s| s.trim_start_matches('0'))
            .collect::<HashSet<_>>()
            .len() as i32
    }
}
```

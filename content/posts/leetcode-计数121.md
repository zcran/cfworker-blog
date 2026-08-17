---
title: "leetcode-计数121"
date: 2026-08-01T10:40:57+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 统计凯撒加密对数目

给你一个由 n 个字符串组成的数组 words。每个字符串的长度均为 m 且仅包含小写英文字母。

如果我们可以通过执行以下操作任意次数（可能为零次）使得两个字符串 s 和 t 变得 相等，则称这两个字符串是 相似 的。

· 选择 s 或 t 。
· 将所选字符串中的 每个 字母替换为字母表中的下一个字母（循环替换）。'z' 之后的下一个字母是 'a'。

计算满足以下条件的下标对 (i, j) 的数量：

· i < j
· words[i] 和 words[j] 是 相似 的。

返回一个整数，表示此类下标对的数量。


```
use std::collections::HashMap;

impl Solution {
    /// 统计相似字符串对的数量。
    /// 两个字符串相似，当且仅当它们对应位置的字母偏移模式相同。
    pub fn count_pairs(words: Vec<String>) -> i64 {
        let mut freq = HashMap::new();
        let mut ans = 0i64;

        for word in &words {
            let base = word.as_bytes()[0] as i32;
            // 将每个字符相对于首字符的循环偏移转为小写字母，构成模式串
            let pattern: String = word.as_bytes().iter()
                .map(|&b| {
                    let offset = (b as i32 - base).rem_euclid(26) as u8;
                    (offset + b'a') as char
                })
                .collect();

            ans += *freq.get(&pattern).unwrap_or(&0) as i64;
            *freq.entry(pattern).or_insert(0) += 1;
        }

        ans
    }
}
```

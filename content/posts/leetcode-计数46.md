---
title: "leetcode-计数46"
date: 2026-08-01T10:40:53+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 检查是否所有字符出现次数相同

给你一个字符串 s ，如果 s 是一个 好 字符串，请你返回 true ，否则请返回 false 。

如果 s 中出现过的 所有 字符的出现次数 相同 ，那么我们称字符串 s 是 好 字符串。


```
impl Solution {
    pub fn are_occurrences_equal(s: String) -> bool {
        // 统计每个字符出现次数
        let mut count = [0; 26];
        for &b in s.as_bytes() {
            count[(b - b'a') as usize] += 1;
        }

        // 找到第一个出现的字符频次作为基准
        let base = count.iter().find(|&&c| c > 0).unwrap_or(&0);

        // 所有出现过的字符频次必须与基准相同
        count.iter().all(|&c| c == 0 || c == *base)
    }
}
```

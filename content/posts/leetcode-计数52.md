---
title: "leetcode-计数52"
date: 2026-08-01T10:40:53+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 统计出现过一次的公共字符串

给你两个字符串数组 words1 和 words2 ，请你返回在两个字符串数组中 都恰好出现一次 的字符串的数目。


```
use std::collections::HashMap;

impl Solution {
    pub fn count_words(words1: Vec<String>, words2: Vec<String>) -> i32 {
        // 统计两个数组中每个字符串的出现次数
        let mut cnt1 = HashMap::with_capacity(words1.len());
        let mut cnt2 = HashMap::with_capacity(words2.len());

        for word in words1 {
            *cnt1.entry(word).or_insert(0) += 1;
        }
        for word in words2 {
            *cnt2.entry(word).or_insert(0) += 1;
        }

        // 只遍历较小的哈希表以提高效率
        let (smaller, larger) = if cnt1.len() <= cnt2.len() {
            (&cnt1, &cnt2)
        } else {
            (&cnt2, &cnt1)
        };

        // 统计在两个数组中都恰好出现一次的字符串数量
        let mut ans = 0;
        for (word, &count1) in smaller {
            if count1 == 1 && larger.get(word) == Some(&1) {
                ans += 1;
            }
        }
        ans
    }
}
```

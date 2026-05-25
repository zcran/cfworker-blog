---
title: "leetcode-队列4"
date: 2026-05-23T09:21:37+08:00
tags: ["leetcode", "队列"]
draft: false
---


## 字符串中的第一个唯一字符


给定一个字符串 s ，找到 它的第一个不重复的字符，并返回它的索引 。如果不存在，则返回 -1 。


```
impl Solution {
    pub fn first_uniq_char(s: String) -> i32 {
        let mut cnt = [0; 26];
        for &b in s.as_bytes() {
            cnt[(b - b'a') as usize] += 1;
        }
        s.bytes()
            .enumerate()
            .find(|&(_, b)| cnt[(b - b'a') as usize] == 1)
            .map(|(i, _)| i as i32)
            .unwrap_or(-1)
    }
}
```

预计算每个字符在 cnt 中的索引

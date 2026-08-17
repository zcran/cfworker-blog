---
title: "leetcode-计数109"
date: 2026-08-01T10:40:57+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 找到字符串中合法的相邻数字

给你一个只包含数字的字符串 s 。如果 s 中两个 相邻 的数字满足以下条件，我们称它们是 合法的 ：

前面的数字 不等于 第二个数字。

两个数字在 s 中出现的次数 恰好 分别等于这个数字本身。

请你从左到右遍历字符串 s ，并返回最先找到的 合法 相邻数字。如果这样的相邻数字不存在，请你返回一个空字符串。


```
impl Solution {
    pub fn find_valid_pair(s: String) -> String {
        let b = s.as_bytes();
        if b.len() < 2 { return String::new(); }

        let mut cnt = [0u8; 10];
        for &c in b { cnt[(c - b'0') as usize] += 1; }

        for i in 1..b.len() {
            let x = (b[i - 1] - b'0') as usize;
            let y = (b[i] - b'0') as usize;
            if x != y && cnt[x] == x as u8 && cnt[y] == y as u8 {
                return unsafe { String::from_utf8_unchecked(vec![b[i - 1], b[i]]) };
            }
        }
        String::new()
    }
}
```

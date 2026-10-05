---
title: "leetcode-双指针41"
date: 2026-09-05T09:53:03+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 计数二进制子串

给定一个字符串 s，统计并返回具有相同数量 0 和 1 的非空（连续）子字符串的数量，并且这些子字符串中的所有 0 和所有 1 都是成组连续的。

重复出现（不同位置）的子串也要统计它们出现的次数。


```
impl Solution {
    pub fn count_binary_substrings(s: String) -> i32 {
        let bytes = s.as_bytes();
        let n = bytes.len();

        let mut ans = 0;
        let mut prev_count = 0;
        let mut i = 0;

        while i < n {
            let c = bytes[i];
            let mut curr_count = 0;
            // 统计当前连续字符段的长度
            while i < n && bytes[i] == c {
                curr_count += 1;
                i += 1;
            }
            // 当前段与前一段能组成的有效子串数量为二者长度的较小值
            if prev_count > 0 {
                ans += curr_count.min(prev_count);
            }
            prev_count = curr_count;
        }

        ans
    }
}
```

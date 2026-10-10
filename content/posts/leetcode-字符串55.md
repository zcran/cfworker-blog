---
title: "leetcode-字符串55"
date: 2026-10-10T12:56:56+08:00
tags: ["leetcode", "字符串"]
draft: false
---


## 最长的字母序连续子字符串的长度

字母序连续字符串 是由字母表中连续字母组成的字符串。换句话说，字符串 "abcdefghijklmnopqrstuvwxyz" 的任意子字符串都是 字母序连续字符串 。

例如，"abc" 是一个字母序连续字符串，而 "acb" 和 "za" 不是。

给你一个仅由小写英文字母组成的字符串 s ，返回其 最长 的 字母序连续子字符串 的长度。




```
impl Solution {
    pub fn longest_continuous_substring(s: String) -> i32 {
        // 遍历相邻字符，若后一个比前一个大 1 则当前连续段 +1，否则重置
        let (mut best, mut cur) = (1, 1);
        for w in s.as_bytes().windows(2) {
            cur = if w[1] == w[0] + 1 { cur + 1 } else { 1 };
            best = best.max(cur);
        }
        best
    }
}
```

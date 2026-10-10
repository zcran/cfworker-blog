---
title: "leetcode-哈希表83"
date: 2026-10-09T20:08:01+08:00
tags: ["leetcode", "哈希表"]
draft: false
---


## 不同首字母的子字符串数目

给你一个由小写英文字母组成的字符串 s。

返回一个整数，表示可以将 s 划分为子字符串的最大数量，使得每个 子字符串 都以一个 不同 字符开头（即，任意两个子字符串的首字符不能相同）。

子字符串 是字符串中一个连续、非空字符序列。


```
impl Solution {
    pub fn max_distinct(s: String) -> i32 {
        // 每个子字符串只能以不同字符开头，
        // 因此最大划分数 = s 中出现的不同字符种类数。
        // 用 26 位掩码统计出现过的字符，最后数一数位数即可。
        let mut mask = 0u32;
        for b in s.bytes() {
            mask |= 1 << (b - b'a');
        }
        mask.count_ones() as i32
    }
}
```

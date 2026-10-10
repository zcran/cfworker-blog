---
title: "leetcode-哈希表67"
date: 2026-10-09T20:07:59+08:00
tags: ["leetcode", "哈希表"]
draft: false
---


## 统计特殊字母的数量 I

给你一个字符串 word。如果 word 中同时存在某个字母的小写形式和大写形式，则称这个字母为 特殊字母 。

返回 word 中 特殊字母 的数量。


```
impl Solution {
    pub fn number_of_special_chars(word: String) -> i32 {
        // 用一个 26 位掩码记录出现过的小写字母，另一个记录大写字母
        let (mut lower, mut upper) = (0u32, 0u32);
        for b in word.bytes() {
            if b.is_ascii_lowercase() {
                lower |= 1 << (b - b'a');
            } else {
                upper |= 1 << (b - b'A');
            }
        }
        // 同时出现大小写的字母，即两个掩码按位与后置位的个数
        (lower & upper).count_ones() as i32
    }
}
```

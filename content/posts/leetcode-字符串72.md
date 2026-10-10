---
title: "leetcode-字符串72"
date: 2026-10-10T12:56:57+08:00
tags: ["leetcode", "字符串"]
draft: false
---


## 两个字符串的排列差

给你两个字符串 s 和 t，每个字符串中的字符都不重复，且 t 是 s 的一个排列。

排列差 定义为 s 和 t 中每个字符在两个字符串中位置的绝对差值之和。

返回 s 和 t 之间的 排列差 。


```
impl Solution {
    pub fn find_permutation_difference(s: String, t: String) -> i32 {
        // 记录 s 中每个字符的下标
        let mut pos = [0i32; 26];
        for (i, b) in s.bytes().enumerate() {
            pos[(b - b'a') as usize] = i as i32;
        }

        // 累加 t 中每个字符与 s 中下标的绝对差
        t.bytes()
            .enumerate()
            .map(|(i, b)| (i as i32 - pos[(b - b'a') as usize]).abs())
            .sum()
    }
}
```

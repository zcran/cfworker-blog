---
title: "leetcode-贪心86"
date: 2026-10-03T12:55:39+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 转换字符串的最少操作次数

给你一个字符串 s ，由 n 个字符组成，每个字符不是 'X' 就是 'O' 。

一次 操作 定义为从 s 中选出 三个连续字符 并将选中的每个字符都转换为 'O' 。注意，如果字符已经是 'O' ，只需要保持 不变 。

返回将 s 中所有字符均转换为 'O' 需要执行的 最少 操作次数。


```
impl Solution {
    pub fn minimum_moves(s: String) -> i32 {
        let bytes = s.as_bytes();
        let mut moves = 0;
        let mut i = 0;

        // 从左往右扫描，遇到 'X' 就在此位置执行一次操作，
        // 该操作覆盖 i..i+3 三个字符，直接跳过这三格
        while i < bytes.len() {
            if bytes[i] == b'X' {
                moves += 1;
                i += 3;
            } else {
                i += 1;
            }
        }
        moves
    }
}
```

---
title: "leetcode-贪心133"
date: 2026-10-03T12:55:43+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 消除相邻近似相等字符

给你一个下标从 0 开始的字符串 word 。

一次操作中，你可以选择 word 中任意一个下标 i ，将 word[i] 修改成任意一个小写英文字母。

请你返回消除 word 中所有相邻 近似相等 字符的 最少 操作次数。

两个字符 a 和 b 如果满足 a == b 或者 a 和 b 在字母表中是相邻的，那么我们称它们是 近似相等 字符。


```
impl Solution {
    pub fn remove_almost_equal_characters(word: String) -> i32 {
        // 贪心：从左到右扫描，遇到一对近似相等字符就改掉后一个，
        // 并跳过这两个字符（改后一个不会影响与更后面字符的关系）。
        let w = word.as_bytes();
        let mut ops = 0;
        let mut i = 1;

        while i < w.len() {
            if (w[i] as i32 - w[i - 1] as i32).abs() < 2 {
                ops += 1;
                i += 2; // 后一个字符被修改，跳过这对
            } else {
                i += 1;
            }
        }
        ops
    }
}
```

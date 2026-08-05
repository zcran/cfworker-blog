---
title: "leetcode-计数32"
date: 2026-08-01T10:40:52+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 确定两个字符串是否接近

如果可以使用以下操作从一个字符串得到另一个字符串，则认为两个字符串 接近 ：

· 操作 1：交换任意两个 现有 字符。
    · 例如，abcde -> aecdb

· 操作 2：将一个 现有 字符的每次出现转换为另一个 现有 字符，并对另一个字符执行相同的操作。
    · 例如，aacabb -> bbcbaa（所有 a 转化为 b ，而所有的 b 转换为 a ）

你可以根据需要对任意一个字符串多次使用这两种操作。

给你两个字符串，word1 和 word2 。如果 word1 和 word2 接近 ，就返回 true ；否则，返回 false 。


```
impl Solution {
    pub fn close_strings(word1: String, word2: String) -> bool {
        // 长度不同肯定不接近
        if word1.len() != word2.len() {
            return false;
        }

        let mut cnt1 = [0; 26];
        let mut cnt2 = [0; 26];
        let mut mask1 = 0;
        let mut mask2 = 0;

        // 统计两个字符串的字符频次
        for &b in word1.as_bytes() {
            let idx = (b - b'a') as usize;
            cnt1[idx] += 1;
            mask1 |= 1 << idx;
        }
        for &b in word2.as_bytes() {
            let idx = (b - b'a') as usize;
            cnt2[idx] += 1;
            mask2 |= 1 << idx;
        }

        // 1. 两个字符串必须包含相同的字符集（操作2可以交换字符身份）
        if mask1 != mask2 {
            return false;
        }

        // 2. 频次集合必须相同（操作2可以重新分配频次，操作1可以任意排列）
        cnt1.sort_unstable();
        cnt2.sort_unstable();
        cnt1 == cnt2
    }
}
```

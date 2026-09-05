---
title: "leetcode-前缀和121"
date: 2026-08-25T10:02:53+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 相等子字符串分数

给你一个由小写英文字母组成的字符串 s。

一个字符串的 得分 是其字符在字母表中的位置之和，其中 'a' = 1，'b' = 2，...，'z' = 26。

请你判断是否存在一个下标 i，使得该字符串可以被拆分成两个 非空子字符串 s[0..i] 和 s[(i + 1)..(n - 1)]，且它们的得分 相等 。

如果存在这样的拆分，则返回 true，否则返回 false。

一个 子字符串 是字符串中 非空 的连续字符序列。


```
impl Solution {
    pub fn score_balance(s: String) -> bool {
        // 计算总得分：字符位置 'a'=1, ..., 'z'=26
        // 可以用 b & 31 快速计算，因为 'a'=97 的二进制低5位为1
        let total: i32 = s.bytes().map(|b| (b & 31) as i32).sum();

        let mut left = 0;
        // 遍历到倒数第二个字符，因为两个子字符串都必须非空
        for b in s.bytes().take(s.len() - 1) {
            left += (b & 31) as i32;
            if left * 2 == total {
                return true;
            }
        }

        false
    }
}
```

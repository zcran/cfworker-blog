---
title: "leetcode-计数71"
date: 2026-08-01T10:40:54+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 第一个出现两次的字母

给你一个由小写英文字母组成的字符串 s ，请你找出并返回第一个出现 两次 的字母。

注意：

如果 a 的 第二次 出现比 b 的 第二次 出现在字符串中的位置更靠前，则认为字母 a 在字母 b 之前出现两次。

s 包含至少一个出现两次的字母。


```
impl Solution {
    pub fn repeated_character(s: String) -> char {
        // 使用位图记录已出现的字母（26位足够）
        let mut seen = 0u32;

        for &ch in s.as_bytes() {
            let bit = 1 << (ch - b'a');
            // 如果该位已置1，说明当前字符是第二次出现
            if seen & bit != 0 {
                return ch as char;
            }
            seen |= bit; // 标记该字母已出现
        }

        // 题目保证至少有一个重复字母，这里不会执行到
        unreachable!()
    }
}
```

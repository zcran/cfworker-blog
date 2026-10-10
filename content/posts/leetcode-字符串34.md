---
title: "leetcode-字符串34"
date: 2026-10-10T12:56:53+08:00
tags: ["leetcode", "字符串"]
draft: false
---


## 生成交替二进制字符串的最少操作数

给你一个仅由字符 '0' 和 '1' 组成的字符串 s 。一步操作中，你可以将任一 '0' 变成 '1' ，或者将 '1' 变成 '0' 。

交替字符串 定义为：如果字符串中不存在相邻两个字符相等的情况，那么该字符串就是交替字符串。例如，字符串 "010" 是交替字符串，而字符串 "0100" 不是。

返回使 s 变成 交替字符串 所需的 最少 操作数。


```
impl Solution {
    pub fn min_operations(s: String) -> i32 {
        // 统计与模式 "0101..." 不同的位数
        // 另一种模式 "1010..." 的不同位数即为 len - diff
        let mut diff = 0u32;
        for (i, b) in s.bytes().enumerate() {
            if (b - b'0') != (i & 1) as u8 {
                diff += 1;
            }
        }
        diff.min(s.len() as u32 - diff) as i32
    }
}
```

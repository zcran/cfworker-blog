---
title: "leetcode-哈希表45"
date: 2026-10-09T20:07:58+08:00
tags: ["leetcode", "哈希表"]
draft: false
---


## 字符串中第二大的数字

给你一个混合字符串 s ，请你返回 s 中 第二大 的数字，如果不存在第二大的数字，请你返回 -1 。

混合字符串 由小写英文字母和数字组成。




```
impl Solution {
    pub fn second_highest(s: String) -> i32 {
        // 维护最大和次大数字（初始为 -1），遍历字符更新。
        let (mut first, mut second) = (-1i32, -1i32);
        for b in s.bytes() {
            if b.is_ascii_digit() {
                let d = (b - b'0') as i32;
                if d > first {
                    second = first;
                    first = d;
                } else if d < first && d > second {
                    second = d;
                }
            }
        }
        second
    }
}
```

---
title: "leetcode-双指针24"
date: 2026-09-05T09:53:02+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 判断子序列

给定字符串 s 和 t ，判断 s 是否为 t 的子序列。

字符串的一个子序列是原始字符串删除一些（也可以不删除）字符而不改变剩余字符相对位置形成的新字符串。（例如，"ace"是"abcde"的一个子序列，而"aec"不是）。

进阶：

如果有大量输入的 S，称作 S1, S2, ... , Sk 其中 k >= 10亿，你需要依次检查它们是否为 T 的子序列。在这种情况下，你会怎样改变代码？


```
impl Solution {
    pub fn is_subsequence(s: String, t: String) -> bool {
        // 空字符串是任何字符串的子序列
        if s.is_empty() {
            return true;
        }

        let s_bytes = s.as_bytes();
        let mut si = 0;

        // 遍历 t，逐个匹配 s 中的字符
        for c in t.bytes() {
            if c == s_bytes[si] {
                si += 1;
                if si == s_bytes.len() {
                    return true;
                }
            }
        }

        false
    }
}
```

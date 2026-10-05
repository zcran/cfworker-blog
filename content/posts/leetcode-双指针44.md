---
title: "leetcode-双指针44"
date: 2026-09-05T09:53:03+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 在 LR 字符串中交换相邻字符

在一个由 'L' , 'R' 和 'X' 三个字符组成的字符串（例如"RXXLRXRXL"）中进行移动操作。一次移动操作指用一个 "LX" 替换一个 "XL"，或者用一个 "XR" 替换一个 "RX"。现给定起始字符串 start 和结束字符串 result，请编写代码，当且仅当存在一系列移动操作使得 start 可以转换成 result 时， 返回 True。


```
impl Solution {
    pub fn can_transform(start: String, end: String) -> bool {
        let s = start.as_bytes();
        let e = end.as_bytes();
        let (mut i, mut j) = (0, 0);
        let (m, n) = (s.len(), e.len());

        while i < m || j < n {
            // 跳过所有 'X'，找到下一个非 'X' 字符
            while i < m && s[i] == b'X' { i += 1; }
            while j < n && e[j] == b'X' { j += 1; }

            // 如果一方已结束，另一方也必须结束
            if i == m || j == n {
                return i == m && j == n;
            }

            // 非 'X' 字符必须相同，且 'L' 只能左移，'R' 只能右移
            if s[i] != e[j] {
                return false;
            }
            if s[i] == b'L' && i < j {
                return false; // 'L' 不能向右移动
            }
            if s[i] == b'R' && i > j {
                return false; // 'R' 不能向左移动
            }

            i += 1;
            j += 1;
        }

        true
    }
}
```

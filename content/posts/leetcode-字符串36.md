---
title: "leetcode-字符串36"
date: 2026-10-10T12:56:54+08:00
tags: ["leetcode", "字符串"]
draft: false
---


## 检查二进制字符串字段

给你一个二进制字符串 s ，该字符串 不含前导零 。

如果 s 包含 零个或一个由连续的 '1' 组成的字段 ，返回 true​​​ 。否则，返回 false 。




```
impl Solution {
    pub fn check_ones_segment(s: String) -> bool {
        !s.contains("01")
    }
}
```

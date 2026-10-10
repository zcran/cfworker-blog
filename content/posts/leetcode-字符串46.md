---
title: "leetcode-字符串46"
date: 2026-10-10T12:56:55+08:00
tags: ["leetcode", "字符串"]
draft: false
---


## 检查是否所有 A 都在 B 之前

给你一个 仅 由字符 'a' 和 'b' 组成的字符串  s 。如果字符串中 每个 'a' 都出现在 每个 'b' 之前，返回 true ；否则，返回 false 。



```
impl Solution {
    pub fn check_string(s: String) -> bool {
        // 若所有 'a' 都在 'b' 之前，则字符串中不存在子串 "ba"
        !s.contains("ba")
    }
}
```

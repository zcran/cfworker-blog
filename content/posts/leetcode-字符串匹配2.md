---
title: "leetcode-字符串匹配2"
date: 2026-04-20T20:32:10+08:00
tags: ["leetcode", "字符串匹配"]
draft: false
---


## 重复的子字符串

给定一个非空的字符串 s ，检查是否可以通过由它的一个子串重复多次构成。

```
impl Solution {
    pub fn repeated_substring_pattern(s: String) -> bool {
        // 边界条件直接返回 false
        if s.len() < 2 {
            return false;
        }

        // 通过迭代器链式操作：重复、切片、包含检查
        s.repeat(2)                 // "ab" -> "abab"
            .get(1..2 * s.len() - 1) // 切掉首尾字符
            .map_or(false, |sub| sub.contains(&s))
    }
}
```

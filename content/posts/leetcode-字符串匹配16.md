---
title: "leetcode-字符串匹配16"
date: 2026-04-20T20:32:10+08:00
tags: ["leetcode", "字符串匹配"]
draft: false
---


## 子字符串匹配模式

给你一个字符串 s 和一个模式字符串 p ，其中 p 恰好 包含 一个 '*' 符号。

p 中的 '*' 符号可以被替换为零个或多个字符组成的任意字符序列。

如果 p 可以变成 s 的 子字符串，那么返回 true ，否则返回 false 。


```
impl Solution {
    pub fn has_match(mut s: String, p: String) -> bool {
        for pt in p.split("*") {
            let Some(pos) = s.find(pt) else {
                return false;
            };
            s = s.split_off(pos + pt.len());
        }
        true
    }
}
```

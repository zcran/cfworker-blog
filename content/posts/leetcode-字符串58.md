---
title: "leetcode-字符串58"
date: 2026-10-10T12:56:56+08:00
tags: ["leetcode", "字符串"]
draft: false
---


## 数组中字符串的最大值

一个由字母和数字组成的字符串的 值 定义如下：

如果字符串 只 包含数字，那么值为该字符串在 10 进制下的所表示的数字。
否则，值为字符串的 长度 。

给你一个字符串数组 strs ，每个字符串都只由字母和数字组成，请你返回 strs 中字符串的 最大值 。




```
impl Solution {
    pub fn maximum_value(strs: Vec<String>) -> i32 {
        strs.iter()
            .map(|s| {
                // 纯数字取数值，否则取长度
                s.parse::<i32>().unwrap_or(s.len() as i32)
            })
            .max()
            .unwrap()
    }
}
```

---
title: "leetcode-字符串6"
date: 2026-10-10T12:56:51+08:00
tags: ["leetcode", "字符串"]
draft: false
---


## Excel 表列序号

给你一个字符串 columnTitle ，表示 Excel 表格中的列名称。返回 该列名称对应的列序号 。

例如：

A -> 1
B -> 2
C -> 3
...
Z -> 26
AA -> 27
AB -> 28
...


```
impl Solution {
    pub fn title_to_number(column_title: String) -> i32 {
        // 26 进制：逐位累加，A 视为 1，故偏移 b'A' - 1
        column_title
            .bytes()
            .fold(0i32, |acc, b| acc * 26 + (b - b'A' + 1) as i32)
    }
}
```

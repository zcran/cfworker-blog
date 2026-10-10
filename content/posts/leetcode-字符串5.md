---
title: "leetcode-字符串5"
date: 2026-10-10T12:56:51+08:00
tags: ["leetcode", "字符串"]
draft: false
---


## Excel 表列名称

给你一个整数 columnNumber ，返回它在 Excel 表中相对应的列名称。

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
    pub fn convert_to_title(mut column_number: i32) -> String {
        let mut bytes = Vec::new();

        // 本质是 1~26 的「26 进制」，每次取 (n-1)%26 得到当前最低位字母
        while column_number > 0 {
            column_number -= 1;
            bytes.push(b'A' + (column_number % 26) as u8);
            column_number /= 26;
        }

        // 构造时是从低位到高位，反转后即为结果
        bytes.reverse();
        String::from_utf8(bytes).unwrap()
    }
}
```

---
title: "leetcode-字符串62"
date: 2026-10-10T12:56:56+08:00
tags: ["leetcode", "字符串"]
draft: false
---


## 移除字符串中的尾随零

给你一个用字符串表示的正整数 num ，请你以字符串形式返回不含尾随零的整数 num 。




```
impl Solution {
    pub fn remove_trailing_zeros(num: String) -> String {
        // 找到第一个非尾随零的末尾位置，直接截断
        let end = num.trim_end_matches('0').len();
        num[..end].to_string()
    }
}
```

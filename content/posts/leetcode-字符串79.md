---
title: "leetcode-字符串79"
date: 2026-10-10T12:56:58+08:00
tags: ["leetcode", "字符串"]
draft: false
---


## 最大的偶数

给你一个仅由字符'1'和'2'组成的字符串s。

你可以删除字符串s中的任意数量的字符，但必须保持剩余字符的顺序不变。

返回可以表示 偶数 整数的 最大结果字符串 。如果不存在这样的字符串，则返回空字符串""。




```
impl Solution {
    pub fn largest_even(s: String) -> String {
        // 去掉末尾所有的 '1'，剩下的字符串以 '2' 结尾（或为空），即为最大偶数
        let end = s.trim_end_matches('1').len();
        s[..end].to_string()
    }
}
```

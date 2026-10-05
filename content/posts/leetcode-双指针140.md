---
title: "leetcode-双指针140"
date: 2026-09-05T09:53:09+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 一次编辑

字符串有三种编辑操作:插入一个英文字符、删除一个英文字符或者替换一个英文字符。 给定两个字符串，编写一个函数判定它们是否只需要一次(或者零次)编辑。


```
impl Solution {
    pub fn one_edit_away(first: String, second: String) -> bool {
        let (a, b) = (first.as_bytes(), second.as_bytes());
        // 保证 a 是较长的那个
        let (a, b) = if a.len() < b.len() { (b, a) } else { (a, b) };

        let (m, n) = (a.len(), b.len());
        if m - n > 1 {
            return false;
        }

        // 找到第一个不同字符的位置；若全部相同则 i == n
        let i = (0..n).find(|&i| a[i] != b[i]).unwrap_or(n);

        if m == n {
            // 等长：跳过 i 后比较剩余部分（用 get 避免越界）
            a.get(i + 1..) == b.get(i + 1..)
        } else {
            // 长度差 1：跳过 a 中多出的那个字符后比较
            a.get(i + 1..) == b.get(i..)
        }
    }
}
```

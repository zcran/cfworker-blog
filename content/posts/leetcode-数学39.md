---
title: "leetcode-数学39"
date: 2026-10-07T20:39:30+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 反转两次的数字

反转 一个整数意味着倒置它的所有位。

例如，反转 2021 得到 1202 。反转 12300 得到 321 ，不保留前导零 。

给你一个整数 num ，反转 num 得到 reversed1 ，接着反转 reversed1 得到 reversed2 。如果 reversed2 等于 num ，返回 true ；否则，返回 false 。




```
impl Solution {
    pub fn is_same_after_reversals(num: i32) -> bool {
        // 反转两次等价于原数，当且仅当 num 末尾没有 0（不产生前导零丢失）。
        // 特例：num == 0 本身也满足。
        num == 0 || num % 10 != 0
    }
}
```

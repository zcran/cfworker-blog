---
title: "leetcode-数论2"
date: 2026-06-20T11:09:54+08:00
tags: ["leetcode", "数论"]
draft: false
---


## 各位相加

给定一个非负整数 num，反复将各个位上的数字相加，直到结果为一位数。返回这个结果。


```
impl Solution {
    pub fn add_digits(num: i32) -> i32 {
        (num - 1) % 9 + 1
    }
}
```

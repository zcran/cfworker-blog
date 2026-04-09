---
title: "leetcode-脑经急转弯18"
date: 2026-03-24T20:55:57+08:00
tags: ["leetcode", "脑筋急转弯"]
draft: false
---

##  最大数值

编写一个方法，找出两个数字a和b中最大的那一个。不得使用if-else或其他比较运算符。

```
impl Solution {
    pub fn maximum(a: i32, b: i32) -> i32 {
        // Passed 0ms 1.9mb
        let k = ((a as i64 - b as i64) >> 63) as i32 + 1;
        a * (k & 1) + b * (!k & 1)
    }
}
```

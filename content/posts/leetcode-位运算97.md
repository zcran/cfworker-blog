---
title: "leetcode-位运算97"
date: 2026-09-24T10:33:40+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 整数转换

整数转换。编写一个函数，确定需要改变几个位才能将整数 A 转成整数 B。

```
impl Solution {
    pub fn convert_integer(a: i32, b: i32) -> i32 {
        // A 与 B 不同的位，异或后为 1；统计其中 1 的个数即为需要改变的位数。
        (a ^ b).count_ones() as i32
    }
}
```

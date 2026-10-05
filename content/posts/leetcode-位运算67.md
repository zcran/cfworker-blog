---
title: "leetcode-位运算67"
date: 2026-09-24T10:33:38+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 使两个整数相等的位更改次数

给你两个正整数 n 和 k。

你可以选择 n 的 二进制表示 中任意一个值为 1 的位，并将其改为 0。

返回使得 n 等于 k 所需要的更改次数。如果无法实现，返回 -1。


```
impl Solution {
    pub fn min_changes(n: i32, k: i32) -> i32 {
        // n 只能把 1 改为 0，无法把 0 改为 1
        // 因此若 k 在 n 为 0 的位上为 1，则无解
        if k & !n != 0 {
            return -1;
        }
        // 统计 n 中为 1 但 k 中为 0 的位数，即为需要的更改次数
        (n & !k).count_ones() as i32
    }
}
```

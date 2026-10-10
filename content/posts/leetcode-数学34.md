---
title: "leetcode-数学34"
date: 2026-10-07T20:39:30+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 判断一个数字是否可以表示成三的幂的和

给你一个整数 n ，如果你可以将 n 表示成若干个不同的三的幂之和，请你返回 true ，否则请返回 false 。

对于一个整数 y ，如果存在整数 x 满足 y == 3x ，我们称这个整数 y 是三的幂。


```
impl Solution {
    pub fn check_powers_of_three(mut n: i32) -> bool {
        // 三进制表示中每位只能是 0 或 1（每个三的幂最多用一次），
        // 若出现 2 则无法用不同的三的幂之和表示。
        while n > 0 {
            if n % 3 == 2 {
                return false;
            }
            n /= 3;
        }
        true
    }
}
```

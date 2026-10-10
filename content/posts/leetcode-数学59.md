---
title: "leetcode-数学59"
date: 2026-10-07T20:39:32+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 哈沙德数

如果一个整数能够被其各个数位上的数字之和整除，则称之为 哈沙德数（Harshad number）。给你一个整数 x 。如果 x 是 哈沙德数 ，则返回 x 各个数位上的数字之和，否则，返回 -1 。


```
impl Solution {
    pub fn sum_of_the_digits_of_harshad_number(x: i32) -> i32 {
        // 求 x 各位数字之和
        let mut digit_sum = 0;
        let mut y = x;
        while y > 0 {
            digit_sum += y % 10;
            y /= 10;
        }

        // 若 x 能被各位数字之和整除，返回该和；否则返回 -1
        if x % digit_sum == 0 { digit_sum } else { -1 }
    }
}
```

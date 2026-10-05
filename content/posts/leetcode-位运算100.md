---
title: "leetcode-位运算100"
date: 2026-09-24T10:33:40+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 交换数字

编写一个函数，不用临时变量，直接交换numbers = [a, b]中a与b的值。

```
impl Solution {
    pub fn swap_numbers(mut numbers: Vec<i32>) -> Vec<i32> {
        // 不用临时变量，利用异或自反性交换两数：
        //   a ^= b;  b ^= a;  a ^= b;
        // 三步后 numbers[0] 与 numbers[1] 互换。
        numbers[0] ^= numbers[1];
        numbers[1] ^= numbers[0];
        numbers[0] ^= numbers[1];

        numbers
    }
}
```

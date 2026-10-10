---
title: "leetcode-数学47"
date: 2026-10-07T20:39:31+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 交替数字和

给你一个正整数 n 。n 中的每一位数字都会按下述规则分配一个符号：

最高有效位 上的数字分配到 正 号。
剩余每位上数字的符号都与其相邻数字相反。

返回所有数字及其对应符号的和。


```
impl Solution {
    pub fn alternate_digit_sum(n: i32) -> i32 {
        // 最高位取正号，之后符号交替：偶数下标加、奇数下标减。
        // 从最低位开始处理，符号为 (-1)^(剩余位数-1)，
        // 但更简单的是直接按高位到低位，用正负号标志交替翻转。
        let mut sum = 0;
        let mut sign = 1; // 从最高位开始为正

        for c in n.to_string().chars() {
            sum += sign * c.to_digit(10).unwrap() as i32;
            sign = -sign;
        }

        sum
    }
}
```

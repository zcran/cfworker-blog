---
title: "leetcode-数学63"
date: 2026-10-07T20:39:32+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 求出数字答案

给你三个 正 整数 num1 ，num2 和 num3 。

数字 num1 ，num2 和 num3 的数字答案 key 是一个四位数，定义如下：

一开始，如果有数字 少于 四位数，给它补 前导 0 。

答案 key 的第 i 个数位（1 <= i <= 4）为 num1 ，num2 和 num3 第 i 个数位中的 最小 值。

请你返回三个数字 没有 前导 0 的数字答案。


```
impl Solution {
    pub fn generate_key(mut num1: i32, mut num2: i32, mut num3: i32) -> i32 {
        // 逐位取三个数的最低位，取最小值作为 key 的对应位（从低位到高位），
        // 每处理完一位就整体右移一位，直到三个数都处理完（最多 4 位）。
        let mut key = 0;
        let mut place = 1;
        for _ in 0..4 {
            key += (num1 % 10).min(num2 % 10).min(num3 % 10) * place;
            num1 /= 10;
            num2 /= 10;
            num3 /= 10;
            place *= 10;
        }
        key
    }
}
```

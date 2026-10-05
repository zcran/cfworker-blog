---
title: "leetcode-二分查找13"
date: 2026-09-28T12:29:10+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 第 N 位数字

给你一个整数 n ，请你在无限的整数序列 [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, ...] 中找出并返回第 n 位上的数字。


```
impl Solution {
    pub fn find_nth_digit(n: i32) -> i32 {
        let mut n = n as i64;
        let mut len = 1i64;       // 当前数字的位数
        let mut count = 9i64;     // 位数为 len 的所有数字占用的总位数

        // 确定第 n 位落在几位数区间
        while n > count {
            n -= count;
            len += 1;
            count = len * 9 * 10i64.pow(len as u32 - 1);
        }

        // 找到具体的数字
        let start = 10i64.pow(len as u32 - 1); // len 位数的第一个数
        let num = start + (n - 1) / len;

        // 取出该数字中的对应位
        let idx = (n - 1) % len;
        let divisor = 10i64.pow((len - idx - 1) as u32);
        (num / divisor % 10) as i32
    }
}
```

---
title: "leetcode-贪心114"
date: 2026-10-03T12:55:41+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 最小和分割

给你一个正整数 num ，请你将它分割成两个非负整数 num1 和 num2 ，满足：

· num1 和 num2 直接连起来，得到 num 各数位的一个排列。
    · 换句话说，num1 和 num2 中所有数字出现的次数之和等于 num 中所有数字出现的次数。

· num1 和 num2 可以包含前导 0 。

请你返回 num1 和 num2 可以得到的和的 最小 值。

注意：

num 保证没有前导 0 。
num1 和 num2 中数位顺序可以与 num 中数位顺序不同。


```
impl Solution {
    pub fn split_num(num: i32) -> i32 {
        // 取出各位数字并升序排序
        let mut digits = num.to_string().into_bytes();
        digits.sort_unstable();

        // 贪心：排序后按位交替分配给两个数，使高位尽可能小，
        // 从而两数之和最小。每次把当前位加到 a 或 b 的末尾。
        let mut a = 0;
        let mut b = 0;
        for (i, &d) in digits.iter().enumerate() {
            let v = (d - b'0') as i32;
            if i % 2 == 0 {
                a = a * 10 + v;
            } else {
                b = b * 10 + v;
            }
        }
        a + b
    }
}
```

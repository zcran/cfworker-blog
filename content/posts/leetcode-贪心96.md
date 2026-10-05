---
title: "leetcode-贪心96"
date: 2026-10-03T12:55:40+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 拆分数位后四位数字的最小和

给你一个四位 正 整数 num 。请你使用 num 中的 数位 ，将 num 拆成两个新的整数 new1 和 new2 。new1 和 new2 中可以有 前导 0 ，且 num 中 所有 数位都必须使用。

比方说，给你 num = 2932 ，你拥有的数位包括：两个 2 ，一个 9 和一个 3 。一些可能的 [new1, new2] 数对为 [22, 93]，[23, 92]，[223, 9] 和 [2, 329] 。

请你返回可以得到的 new1 和 new2 的 最小 和。




```
impl Solution {
    pub fn minimum_sum(num: i32) -> i32 {
        // 取出各位数字并升序排序
        let mut digits = [0u8; 4];
        let mut n = num;
        for d in digits.iter_mut() {
            *d = (n % 10) as u8;
            n /= 10;
        }
        digits.sort_unstable();

        // 贪心：将最小的两个数字分别放到两个数的十位，
        // 较大的两个数字分别放到两个数的个位，使总和最小。
        10 * (digits[0] as i32 + digits[1] as i32)
            + digits[2] as i32
            + digits[3] as i32
    }
}
```

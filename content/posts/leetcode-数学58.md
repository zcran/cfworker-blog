---
title: "leetcode-数学58"
date: 2026-10-07T20:39:32+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 求出加密整数的和

给你一个整数数组 nums ，数组中的元素都是 正 整数。定义一个加密函数 encrypt ，encrypt(x) 将一个整数 x 中 每一个 数位都用 x 中的 最大 数位替换。比方说 encrypt(523) = 555 且 encrypt(213) = 333 。

请你返回数组中所有元素加密后的 和 。




```
impl Solution {
    pub fn sum_of_encrypted_int(nums: Vec<i32>) -> i32 {
        // 对每个数：找出最大数位 d，并构造与位数相同的全 1 数 base（如 3 位 → 111），
        // 则 encrypt(x) = base * d。
        nums.into_iter()
            .map(|mut num| {
                let (mut max_digit, mut base) = (0, 0);
                while num > 0 {
                    max_digit = max_digit.max(num % 10);
                    base = base * 10 + 1;
                    num /= 10;
                }
                base * max_digit
            })
            .sum()
    }
}
```

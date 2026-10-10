---
title: "leetcode-排序74"
date: 2026-10-05T00:35:04+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 两个数字的最大乘积

给定一个正整数 n。

返回 任意两位数字 相乘所得的 最大 乘积。

注意：如果某个数字在 n 中出现多次，你可以多次使用该数字。




```
impl Solution {
    pub fn max_product(n: i32) -> i32 {
        // 维护最大的两个数字
        let (mut first, mut second) = (0, 0);
        let mut num = n;

        while num > 0 {
            let digit = num % 10;
            if digit > first {
                second = first;
                first = digit;
            } else if digit > second {
                second = digit;
            }
            num /= 10;
        }

        first * second
    }
}
```

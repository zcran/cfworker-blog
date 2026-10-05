---
title: "leetcode-贪心108"
date: 2026-10-03T12:55:41+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 美丽整数的最小增量

给你两个正整数 n 和 target 。

如果某个整数每一位上的数字相加小于或等于 target ，则认为这个整数是一个 美丽整数 。

找出并返回满足 n + x 是 美丽整数 的最小非负整数 x 。生成的输入保证总可以使 n 变成一个美丽整数。


```
impl Solution {
    pub fn make_integer_beautiful(n: i64, target: i32) -> i64 {
        // 数位和
        fn digit_sum(mut x: i64) -> i32 {
            let mut sum = 0;
            while x > 0 {
                sum += (x % 10) as i32;
                x /= 10;
            }
            sum
        }

        if digit_sum(n) <= target {
            return 0;
        }

        // 从小到大逐步进位：把末位变 0 并向前进 1，
        // 直到数位和不超过 target，此时的数即为大于等于 n 的最小美丽整数。
        let original = n;
        let mut n = n;
        let mut place = 10i64;

        while digit_sum(n) > target {
            n = (n / place + 1) * place;
            place *= 10;
        }
        n - original
    }
}
```

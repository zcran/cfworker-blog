---
title: "leetcode-堆43"
date: 2026-08-21T09:54:13+08:00
tags: ["leetcode", "堆"]
draft: false
---


## 按奇偶性交换后的最大数字

给你一个正整数 num 。你可以交换 num 中 奇偶性 相同的任意两位数字（即，都是奇数或者偶数）。

返回交换 任意 次之后 num 的 最大 可能值。


```
impl Solution {
    pub fn largest_integer(num: i32) -> i32 {
        let digits: Vec<i32> = num
            .to_string()
            .chars()
            .map(|c| c.to_digit(10).unwrap() as i32)
            .collect();

        // Separate digits by parity and sort each group in descending order
        let mut odd_digits: Vec<i32> = digits.iter().filter(|&&d| d % 2 == 1).copied().collect();
        let mut even_digits: Vec<i32> = digits.iter().filter(|&&d| d % 2 == 0).copied().collect();
        odd_digits.sort_unstable_by(|a, b| b.cmp(a));
        even_digits.sort_unstable_by(|a, b| b.cmp(a));

        // Reconstruct: for each position, pick the largest remaining digit of matching parity
        let mut odd_idx = 0;
        let mut even_idx = 0;
        let mut result = 0i64;

        for &d in &digits {
            result *= 10;
            if d % 2 == 1 {
                result += odd_digits[odd_idx] as i64;
                odd_idx += 1;
            } else {
                result += even_digits[even_idx] as i64;
                even_idx += 1;
            }
        }

        result as i32
    }
}
```

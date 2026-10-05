---
title: "leetcode-双指针38"
date: 2026-09-05T09:53:03+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 平方数之和

给定一个非负整数 c ，你要判断是否存在两个整数 a 和 b，使得 a^2 + b^2 = c 。


```
impl Solution {
    pub fn judge_square_sum(c: i32) -> bool {
        let c = c as i64;
        let mut left = 0;
        let mut right = (c as f64).sqrt() as i64;

        while left <= right {
            let sum = left * left + right * right;
            if sum == c {
                return true;
            } else if sum < c {
                left += 1;
            } else {
                right -= 1;
            }
        }

        false
    }
}
```

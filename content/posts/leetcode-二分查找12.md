---
title: "leetcode-二分查找12"
date: 2026-09-28T12:29:10+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 有效的完全平方数

给你一个正整数 num 。如果 num 是一个完全平方数，则返回 true ，否则返回 false 。

完全平方数 是一个可以写成某个整数的平方的整数。换句话说，它可以写成某个整数和自身的乘积。

不能使用任何内置的库函数，如  sqrt 。


```
impl Solution {
    pub fn is_perfect_square(num: i32) -> bool {
        // 在 [1, num] 中二分查找平方根
        let (mut left, mut right) = (1, num);
        while left <= right {
            let mid = left + (right - left) / 2;
            // 用除法代替乘法，避免 i32 溢出
            let q = num / mid;
            if q == mid {
                return num % mid == 0;
            } else if q < mid {
                right = mid - 1;
            } else {
                left = mid + 1;
            }
        }
        false
    }
}
```

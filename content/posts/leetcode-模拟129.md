---
title: "leetcode-模拟129"
date: 2026-08-08T11:31:16+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 偶数的按位或运算

给你一个整数数组 nums。

返回数组中所有 偶数 的按位 或 运算结果。

如果 nums 中没有偶数，返回 0。


```
impl Solution {
    pub fn even_number_bitwise_o_rs(nums: Vec<i32>) -> i32 {
        let mut ans = 0;
        for x in nums {
            if x % 2 == 0 {
                ans |= x;
            }
        }
        ans
    }
}
```

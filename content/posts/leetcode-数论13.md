---
title: "leetcode-数论13"
date: 2026-06-20T11:09:55+08:00
tags: ["leetcode", "数论"]
draft: false
---


## 找出数组的最大公约数

给你一个整数数组 nums ，返回数组中最大数和最小数的 最大公约数 。

两个数的 最大公约数 是能够被两个数整除的最大正整数。



```
impl Solution {
    /// 返回数组中最大值和最小值的最大公约数
    pub fn find_gcd(nums: Vec<i32>) -> i32 {
        let (min, max) = nums
            .iter()
            .fold((i32::MAX, i32::MIN), |(min, max), &x| {
                (min.min(x), max.max(x))
            });
        gcd(min.abs(), max.abs())
    }
}

/// 使用欧几里得算法（辗转相除法）计算两个整数的最大公约数
fn gcd(mut a: i32, mut b: i32) -> i32 {
    while b != 0 {
        let temp = b;
        b = a % b;
        a = temp;
    }
    a.abs()
}
```

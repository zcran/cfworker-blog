---
title: "leetcode-数论6"
date: 2026-06-20T11:09:55+08:00
tags: ["leetcode", "数论"]
draft: false
---


## 检查「好数组」

给你一个正整数数组 nums，你需要从中任选一些子集，然后将子集中每一个数乘以一个 任意整数，并求出他们的和。

假如该和结果为 1，那么原数组就是一个「好数组」，则返回 True；否则请返回 False。


```
impl Solution {
    pub fn is_good_array(nums: Vec<i32>) -> bool {
        nums.into_iter().reduce(|mut a, mut b| {
            while a != 0 {
                (a, b) = (b.rem_euclid(a), a);
            }
            b
        }) == Some(1)
    }
}
```

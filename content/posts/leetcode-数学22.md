---
title: "leetcode-数学22"
date: 2026-10-07T20:39:29+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 统计位数为偶数的数字

给你一个整数数组 nums，请你返回其中包含 偶数 个数位的数字的个数。


```
impl Solution {
    /// 统计数组中位数为偶数的数字个数。
    ///
    /// 逐个判断每个数的十进制位数是否为偶数即可。
    /// 这里用整数比较代替 log10 浮点运算，避免精度问题且更快。
    pub fn find_numbers(nums: Vec<i32>) -> i32 {
        nums.iter()
            .filter(|&&num| {
                // 位数为偶数：落在 [10,99]、[1000,9999]、[100000,999999] 等区间
                (10..100).contains(&num)
                    || (1000..10_000).contains(&num)
                    || (100_000..1_000_000).contains(&num)
            })
            .count() as i32
    }
}
```

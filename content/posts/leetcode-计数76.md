---
title: "leetcode-计数76"
date: 2026-08-01T10:40:55+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 反转之后不同整数的数目

给你一个由 正 整数组成的数组 nums 。

你必须取出数组中的每个整数，反转其中每个数位，并将反转后得到的数字添加到数组的末尾。这一操作只针对 nums 中原有的整数执行。

返回结果数组中 不同 整数的数目。


```
impl Solution {
    pub fn count_distinct_integers(nums: Vec<i32>) -> i32 {
        use std::collections::HashSet;

        // 用于存储所有数字（原数和反转数）
        let mut set = HashSet::with_capacity(nums.len() * 2);

        for &num in &nums {
            set.insert(num);
            set.insert(reverse_integer(num));
        }

        set.len() as i32
    }
}

/// 反转整数的数位，例如 123 -> 321，120 -> 21
fn reverse_integer(mut n: i32) -> i32 {
    let mut reversed = 0;
    while n > 0 {
        reversed = reversed * 10 + n % 10;
        n /= 10;
    }
    reversed
}
```

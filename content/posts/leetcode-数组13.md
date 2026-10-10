---
title: "leetcode-数组13"
date: 2026-10-10T20:38:41+08:00
tags: ["leetcode", "数组"]
draft: false
---


## 数组形式的整数加法

整数的 数组形式  num 是按照从左到右的顺序表示其数字的数组。

例如，对于 num = 1321 ，数组形式是 [1,3,2,1] 。

给定 num ，整数的 数组形式 ，和整数 k ，返回 整数 num + k 的 数组形式 。


```
impl Solution {
    /// 求数组形式整数 `num` 与 `k` 之和，返回同样数组形式的结果。
    ///
    /// 从最低位（数组末尾）开始逐位相加，`carry` 同时承载 `k` 的剩余部分
    /// 与进位。每轮取 `carry % 10` 压入结果，`carry /= 10`，
    /// 再从 `num` 取下一位累加。最后反转结果。
    ///
    /// 时间复杂度 O(n)，空间复杂度 O(n)（结果数组）。
    pub fn add_to_array_form(num: Vec<i32>, k: i32) -> Vec<i32> {
        let mut result = Vec::with_capacity(num.len() + 1);
        let mut carry = k;
        let mut i = num.len();

        while carry > 0 || i > 0 {
            if i > 0 {
                i -= 1;
                carry += num[i];
            }
            result.push(carry % 10);
            carry /= 10;
        }

        result.reverse();
        result
    }
}
```

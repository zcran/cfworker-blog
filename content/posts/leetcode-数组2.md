---
title: "leetcode-数组2"
date: 2026-10-10T20:38:40+08:00
tags: ["leetcode", "数组"]
draft: false
---


## 加一

给定一个表示 大整数 的整数数组 digits，其中 digits[i] 是整数的第 i 位数字。这些数字按从左到右，从最高位到最低位排列。这个大整数不包含任何前导 0。

将大整数加 1，并返回结果的数字数组。


```
impl Solution {
    /// 大整数加一。
    ///
    /// 从最低位向最高位遍历：
    /// - 若当前位 < 9，直接加 1 并返回（无需进位）；
    /// - 若当前位 == 9，将其置 0，继续向高位进位；
    /// - 若所有位都是 9，则原数组全变为 0，需在最高位补 1。
    ///
    /// 时间复杂度 O(n)，空间复杂度 O(1)（除全 9 时需扩容一位）。
    pub fn plus_one(mut digits: Vec<i32>) -> Vec<i32> {
        for d in digits.iter_mut().rev() {
            if *d < 9 {
                *d += 1;
                return digits;
            }
            *d = 0;
        }

        // 走到这里说明原数字全是 9，加一后变成 100...00
        digits[0] = 1;
        digits.push(0);
        digits
    }
}
```

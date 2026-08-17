---
title: "leetcode-模拟66"
date: 2026-08-08T11:31:12+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 统计各位数字之和为偶数的整数个数

给你一个正整数 num ，请你统计并返回 小于或等于 num 且各位数字之和为 偶数 的正整数的数目。

正整数的 各位数字之和 是其所有位上的对应数字相加的结果。



```
impl Solution {
    /// 返回 1 到 num 中各位数字之和为偶数的正整数个数。
    ///
    /// 规律：1~num 中大约一半数的各位和为偶数。精确公式为 num/2，
    /// 但当 num 为偶数且其各位数字之和为奇数时，num 本身不应被计入，需减 1。
    pub fn count_even(num: i32) -> i32 {
        let mut n = num;
        let mut sum = 0;
        while n > 0 {
            sum += n % 10;
            n /= 10;
        }
        let mut ans = num / 2;
        if sum % 2 == 1 && num % 2 == 0 {
            ans -= 1;
        }
        ans
    }
}
```

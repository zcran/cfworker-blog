---
title: "leetcode-数学76"
date: 2026-10-07T20:39:33+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 判断整除性

给你一个正整数 n。请判断 n 是否可以被以下两值之和 整除：

n 的 数字和（即其各个位数之和）。

n 的 数字积（即其各个位数之积）。

如果 n 能被该和整除，返回 true；否则，返回 false。


```
impl Solution {
    pub fn check_divisibility(n: i32) -> bool {
        // 统计各位数字之和与各位数字之积
        let (mut digit_sum, mut digit_product) = (0, 1);
        let original = n;
        let mut x = n;

        while x > 0 {
            let d = x % 10;
            x /= 10;
            digit_sum += d;
            digit_product *= d;
        }

        // n 能被 (数字和 + 数字积) 整除
        original % (digit_sum + digit_product) == 0
    }
}
```

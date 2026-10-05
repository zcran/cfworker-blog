---
title: "leetcode-贪心21"
date: 2026-10-03T12:55:35+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 单调递增的数字

当且仅当每个相邻位数上的数字 x 和 y 满足 x <= y 时，我们称这个整数是单调递增的。

给定一个整数 n ，返回 小于或等于 n 的最大数字，且数字呈 单调递增 。


```
impl Solution {
    pub fn monotone_increasing_digits(n: i32) -> i32 {
        // 贪心：从最高位开始，逐位尝试放入 1，得到的形如 111... 的前缀组合
        // 每次叠加 ones 就相当于在当前位上放 1，保证结果各位单调不减
        let mut ones = 111_111_111;
        let mut result = 0;

        for _ in 0..9 {
            // 若加上当前 ones 会超过 n，则缩小 ones 到更低位
            while result + ones > n {
                ones /= 10;
            }
            result += ones;
        }

        result
    }
}
```

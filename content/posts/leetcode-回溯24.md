---
title: "leetcode-回溯24"
date: 2026-07-04T10:22:02+08:00
tags: ["leetcode", "回溯"]
draft: false
---


## 统计各位数字都不同的数字个数


给你一个整数 n ，统计并返回各位数字都不同的数字 x 的个数，其中 0 <= x < 10^n 。

```
impl Solution {
    /// 统计 [0, 10^n) 范围内各位数字都不同的数字个数
    ///
    /// 排列组合思路：
    /// - i 位数 (i >= 1)：最高位有 9 种选择 (1-9)，剩余位从 9 个数字中排列
    /// - 特别注意：0 是单独的一个数，要在最后加上
    pub fn count_numbers_with_unique_digits(n: i32) -> i32 {
        let n = n.min(10) as usize;

        let mut count = 1; // 数字 0
        let mut unique_count = 9; // 当前位数的数量：i=1 时有 9 个（1-9）
        let mut available = 9; // 剩余可用数字个数

        for i in 1..=n {
            count += unique_count;
            // 计算下一位（i+1 位数）的数量
            // 当前 i 位数有 unique_count 个，每个后面可以跟 available 个不同数字
            // 但需要排除当前位已经用过的数字，所以乘 available
            if i < n {
                unique_count *= available;
                available -= 1;
            }
        }

        count
    }
}
```

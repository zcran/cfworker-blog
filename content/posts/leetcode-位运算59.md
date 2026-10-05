---
title: "leetcode-位运算59"
date: 2026-09-24T10:33:38+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 价值和小于等于 K 的最大数字

给你一个整数 k 和一个整数 x 。整数 num 的价值是它的二进制表示中在 x，2x，3x 等位置处 设置位 的数目（从最低有效位开始）。下面的表格包含了如何计算价值的例子。

x	num	Binary Representation	Price
1	13	000001101	3
2	13	000001101	1
2	233	011101001	3
3	13	000001101	1
3	362	101101010	2


num 的 累加价值 是从 1 到 num 的数字的 总 价值。如果 num 的累加价值小于或等于 k 则被认为是 廉价 的。

请你返回 最大 的廉价数字。


```
impl Solution {
    pub fn find_maximum_number(k: i64, x: i32) -> i64 {
        // 累加价值随 num 单调不减，二分查找最大的廉价数字。
        // 上界 (k+1) << x 足够大：仅位置 x 每 2^x 个数才贡献 1，累加价值必超过 k。
        let (mut lo, mut hi) = (1i64, (k + 1) << x);
        while lo < hi {
            let mid = lo + (hi - lo + 1) / 2;
            if Self::accumulated_price(x, mid) > k {
                hi = mid - 1;
            } else {
                lo = mid;
            }
        }
        lo
    }

    // 统计 1..=num 中，二进制第 bit 位（从 0 开始）为 1 的数字个数。
    // 该位以 2^(bit+1) 为周期，每周期前一半为 0、后一半为 1。
    fn accumulated_bit_price(bit: i32, num: i64) -> i64 {
        let period = 1i64 << (bit + 1);
        let half = period >> 1;
        let full = num / period * half;
        let rem = num % period;
        full + (rem - half + 1).max(0)
    }

    // 累加价值：统计位置 x, 2x, 3x, ... 上的 1 的个数。
    // 题目位置从 1 开始编号，位置 p 对应位索引 p-1。
    // 因此需统计位索引 x-1, 2x-1, 3x-1, ... 上的 1 的个数。
    fn accumulated_price(x: i32, num: i64) -> i64 {
        let bits = 64 - num.leading_zeros() as i32; // num 的二进制位数
        let mut sum = 0;
        let mut bit = x - 1;
        while bit < bits {
            sum += Self::accumulated_bit_price(bit, num);
            bit += x;
        }
        sum
    }
}
```

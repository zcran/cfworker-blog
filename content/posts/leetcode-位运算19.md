---
title: "leetcode-位运算19"
date: 2026-09-24T10:33:35+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 二进制表示中质数个计算置位

给你两个整数 left 和 right ，在闭区间 [left, right] 范围内，统计并返回 计算置位位数为质数 的整数个数。

计算置位位数 就是二进制表示中 1 的个数。

例如， 21 的二进制表示 10101 有 3 个计算置位。


```
impl Solution {
    /// 核心思路：
    /// 对区间内每个数统计其二进制中 1 的个数（popcount），
    /// 判断该个数是否为质数。
    ///
    /// 由于 i32 最多 32 位，popcount 范围是 [0, 32]，
    /// 其中的质数只有 {2, 3, 5, 7, 11, 13, 17, 19}，
    /// 用位掩码表示可 O(1) 判断。
    pub fn count_prime_set_bits(left: i32, right: i32) -> i32 {
        // 第 k 位为 1 表示 k 是质数（k ∈ {2,3,5,7,11,13,17,19}）
        const PRIME_MASK: u32 = (1 << 2) | (1 << 3) | (1 << 5) | (1 << 7)
                              | (1 << 11) | (1 << 13) | (1 << 17) | (1 << 19);

        (left..=right)
            .filter(|&x| PRIME_MASK >> x.count_ones() & 1 == 1)
            .count() as i32
    }
}
```

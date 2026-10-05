---
title: "leetcode-位运算16"
date: 2026-09-24T10:33:35+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 汉明距离总和

两个整数的 汉明距离 指的是这两个数字的二进制数对应位不同的数量。

给你一个整数数组 nums，请你计算并返回 nums 中任意两个数之间 汉明距离的总和 。


```
impl Solution {
    /// 核心思路：
    /// 逐位统计。对于第 i 位，设数组中有 `ones` 个数该位为 1，`zeros = n - ones` 个为 0。
    /// 该位对总汉明距离的贡献 = ones * zeros（每对 (1, 0) 组合贡献 1）。
    /// 对所有 32 位求和即为答案。
    ///
    /// 时间复杂度 O(32 * n) = O(n)，空间复杂度 O(1)。
    pub fn total_hamming_distance(nums: Vec<i32>) -> i32 {
        let n = nums.len() as i32;
        let mut total = 0i64;

        for i in 0..32 {
            // 统计第 i 位上 1 的个数
            let ones = nums.iter().filter(|&&x| (x >> i) & 1 == 1).count() as i64;
            total += ones * (n as i64 - ones);
        }

        total as i32
    }
}
```

---
title: "leetcode-位运算7"
date: 2026-09-24T10:33:34+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 丢失的数字

给定一个包含 [0, n] 中 n 个数的数组 nums ，找出 [0, n] 这个范围内没有出现在数组中的那个数。


```
impl Solution {
    /// 核心思路：利用异或的性质
    /// - a ^ a = 0，a ^ 0 = a
    ///
    /// 将 [0, n] 的全部数字与 nums 中的元素一起异或：
    /// 成对出现的数字抵消为 0，最终剩下的就是缺失的那个数。
    ///
    /// 由于 0 与任何数异或都不影响结果，因此可以省略 0，
    /// 只需异或 [1, n] 和 nums 中的所有元素。
    /// 而 1 ^ 2 ^ ... ^ n 有周期为 4 的规律，可 O(1) 算出。
    pub fn missing_number(nums: Vec<i32>) -> i32 {
        let n = nums.len();

        // 1 ^ 2 ^ ... ^ n 的闭式解，按 n % 4 分四种情况
        let xor_range = match n & 3 {
            0 => n,
            1 => 1,
            2 => n + 1,
            _ => 0,
        } as i32;

        // 与 nums 中所有元素异或，剩下的即为缺失值
        xor_range ^ nums.into_iter().fold(0, |acc, x| acc ^ x)
    }
}
```

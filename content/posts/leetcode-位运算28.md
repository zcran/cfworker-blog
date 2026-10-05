---
title: "leetcode-位运算28"
date: 2026-09-24T10:33:35+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 数组异或操作

给你两个整数，n 和 start 。

数组 nums 定义为：nums[i] = start + 2*i（下标从 0 开始）且 n == nums.length 。

请返回 nums 中所有元素按位异或（XOR）后得到的结果。




```
impl Solution {
    /// 返回数组 `[start, start+2, ..., start+2(n-1)]` 所有元素的异或结果。
    ///
    /// 数组元素与 `start` 同奇偶，且公差为 2。
    /// 由异或性质，逐项异或即可；由于每次只依赖前一项，
    /// 直接用迭代器折叠，代码更简洁。
    pub fn xor_operation(n: i32, start: i32) -> i32 {
        (0..n).fold(0, |acc, i| acc ^ (start + 2 * i))
    }
}
```

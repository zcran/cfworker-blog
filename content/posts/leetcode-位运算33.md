---
title: "leetcode-位运算33"
date: 2026-09-24T10:33:36+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 所有数对按位与结果的异或和

列表的 异或和（XOR sum）指对所有元素进行按位 XOR 运算的结果。如果列表中仅有一个元素，那么其 异或和 就等于该元素。

例如，[1,2,3,4] 的 异或和 等于 1 XOR 2 XOR 3 XOR 4 = 4 ，而 [3] 的 异或和 等于 3 。
给你两个下标 从 0 开始 计数的数组 arr1 和 arr2 ，两数组均由非负整数组成。

根据每个 (i, j) 数对，构造一个由 arr1[i] AND arr2[j]（按位 AND 运算）结果组成的列表。其中 0 <= i < arr1.length 且 0 <= j < arr2.length 。

返回上述列表的 异或和 。


```
impl Solution {
    /// 返回所有 `arr1[i] & arr2[j]` 组成的列表的异或和。
    ///
    /// 记 A = arr1 的异或和，B = arr2 的异或和。
    /// 由于异或对按位与满足分配律，所有两两 AND 结果的异或和恰为 `A & B`：
    /// 对任意二进制位，该位上 1 的个数为奇数当且仅当 A 和 B 在该位都为 1。
    pub fn get_xor_sum(arr1: Vec<i32>, arr2: Vec<i32>) -> i32 {
        let xor1 = arr1.iter().fold(0, |acc, &x| acc ^ x);
        let xor2 = arr2.iter().fold(0, |acc, &x| acc ^ x);
        xor1 & xor2
    }
}
```

---
title: "leetcode-位运算103"
date: 2026-09-24T10:33:40+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 消失的两个数字

给定一个数组，包含从 1 到 N 所有的整数，但其中缺了两个数字。你能在 O(N) 时间内只用 O(1) 的空间找到它们吗？

以任意顺序返回这两个数字均可。

```
impl Solution {
    pub fn missing_two(nums: Vec<i32>) -> Vec<i32> {
        // 数组含 1..=n+2 中除两个数外的所有整数，长度为 n。
        // 1. 把数组元素与 1..=n+2 全部异或，得到两个缺失数的异或值 `xor`。
        // 2. 取 `xor` 最低位的 1 作为分组依据，两个缺失数在该位上必不同。
        // 3. 按该位把数组元素和 1..=n+2 分成两组，各自异或即得两个缺失数。

        let n = nums.len() as i32;
        let full = 1..=n + 2;

        // 两个缺失数的异或值
        let xor = nums.iter().fold(0, |acc, &x| acc ^ x)
            ^ full.clone().fold(0, |acc, x| acc ^ x);

        // 分组依据：xor 的最低位 1
        let diff = xor & -xor;

        // 按该位分组异或，得到其中一个缺失数
        let mut a = 0;
        for x in nums.iter().copied().chain(full) {
            if x & diff == 0 {
                a ^= x;
            }
        }

        vec![a, a ^ xor]
    }
}
```

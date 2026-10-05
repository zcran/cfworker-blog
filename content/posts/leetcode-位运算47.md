---
title: "leetcode-位运算47"
date: 2026-09-24T10:33:37+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 相邻值的按位异或

下标从 0 开始、长度为 n 的数组 derived 是由同样长度为 n 的原始 二进制数组 original 通过计算相邻值的 按位异或（⊕）派生而来。

特别地，对于范围 [0, n - 1] 内的每个下标 i ：

如果 i = n - 1 ，那么 derived[i] = original[i] ⊕ original[0]
否则 derived[i] = original[i] ⊕ original[i + 1]

给你一个数组 derived ，请判断是否存在一个能够派生得到 derived 的 有效原始二进制数组 original 。

如果存在满足要求的原始二进制数组，返回 true ；否则，返回 false 。

二进制数组是仅由 0 和 1 组成的数组。


```
impl Solution {
    pub fn does_valid_array_exist(derived: Vec<i32>) -> bool {
        // 将所有等式异或后，每个 original[i] 都出现两次而抵消，
        // 因此 derived 全体异或必须为 0，这也是充分条件。
        derived.iter().fold(0, |acc, &x| acc ^ x) == 0
    }
}
```

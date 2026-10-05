---
title: "leetcode-位运算93"
date: 2026-09-24T10:33:40+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 插入

给定两个整型数字 N 与 M，以及表示比特位置的 i 与 j（i <= j，且从 0 位开始计算）。

编写一种方法，使 M 对应的二进制数字插入 N 对应的二进制数字的第 i ~ j 位区域，不足之处用 0 补齐。具体插入过程如图所示。
![](../pic/1610104070-NuLVQi-05.01.gif)


题目保证从 i 位到 j 位足以容纳 M， 例如： M = 10011，则 i～j 区域至少可容纳 5 位。

```
impl Solution {
    pub fn insert_bits(n: i32, m: i32, i: i32, j: i32) -> i32 {
        // 1. 构造低 (j-i+1) 位全 1 的掩码，再左移 i 位，得到覆盖 [i, j] 的掩码。
        // 2. 取反后与 n 相与，清除 n 在 [i, j] 区间的位。
        // 3. 将 m 左移 i 位后与 n 相或，把 m 填入该区间。
        //
        // 注意：i32 左移位数达到 31 时可能溢出，这里用 wrapping 保证语义一致。
        let width = (j - i + 1) as u32;
        let mask = ((1i32 << width) - 1).wrapping_shl(i as u32);
        (n & !mask) | (m << i)
    }
}
```

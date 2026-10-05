---
title: "leetcode-位运算96"
date: 2026-09-24T10:33:40+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 下一个数

下一个数。给定一个正整数，找出与其二进制表达式中1的个数相同且大小最接近的那两个数（一个略大，一个略小）。

```
impl Solution {
    pub fn find_closed_numbers(num: i32) -> Vec<i32> {
        // 位运算求"下一个更大的同 1 个数"：
        //   设 x = num 的最低位的 1。
        //   将最低位 1 加到 num 上产生进位，得到 y = num + x。
        //   再将 y 中多余的 1 调整到最低位，得到结果。
        // 公式：next = y | (((num & !y) / x) >> 1)
        //
        // 求"上一个更小的同 1 个数"：
        //   对 ~num 求 next，再取反即可（对称性）。
        //
        // 边界：
        //   num == 1：只有一个 1，更大的最小是 2，更小的不存在。
        //   num == i32::MAX：全为 1，无法增加 1 的位数，均不存在。
        //   结果超过 i32 范围视为不存在。

        let next = |n: i32| -> i32 {
            let low = n & -n;          // 最低位的 1
            let added = n + low;       // 进位后
            // 把多余的低位 1 移到最低位
            ((n & !added) / low >> 1) | added
        };

        if num == 1 {
            return vec![2, -1];
        }
        if num == i32::MAX {
            return vec![-1, -1];
        }

        let bigger = next(num);
        let smaller = !next(!num);

        // bigger 溢出（符号位为负）视为不存在
        let bigger = if bigger < 0 { -1 } else { bigger };

        vec![bigger, smaller]
    }
}
```

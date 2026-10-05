---
title: "leetcode-位运算39"
date: 2026-09-24T10:33:36+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 最小异或

给你两个正整数 num1 和 num2 ，找出满足下述条件的正整数 x ：

x 的置位数和 num2 相同，且
x XOR num1 的值 最小

注意 XOR 是按位异或运算。

返回整数 x 。题目保证，对于生成的测试用例， x 是 唯一确定 的。

整数的 置位数 是其二进制表示中 1 的数目。


```
impl Solution {
    /// 返回置位数与 num2 相同、且与 num1 异或值最小的整数 x。
    ///
    /// 设 num1 的置位数为 c1，num2 的置位数为 c2。
    /// - 若 c2 < c1：为使异或最小，应保留 num1 中尽量多的高位 1，
    ///   从最低位开始清除 (c1 - c2) 个 1（`n &= n - 1` 清除最低位的 1）。
    /// - 若 c2 > c1：需额外补充 (c2 - c1) 个 1，且应尽量放在低位，
    ///   从最低位的 0 开始置 1（`n |= n + 1` 将最低的 0 变为 1）。
    pub fn minimize_xor(mut num1: i32, num2: i32) -> i32 {
        let mut ones = num1.count_ones() as i32; // 当前 num1 中 1 的个数
        let target = num2.count_ones() as i32;   // 目标 1 的个数

        // 1 太多：从最低位开始清除多余的 1
        while ones > target {
            num1 &= num1 - 1;
            ones -= 1;
        }

        // 1 太少：从最低位的 0 开始补 1
        while ones < target {
            num1 |= num1 + 1;
            ones += 1;
        }

        num1
    }
}
```

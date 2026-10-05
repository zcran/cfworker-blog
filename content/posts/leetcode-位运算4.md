---
title: "leetcode-位运算4"
date: 2026-09-24T10:33:34+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 数字范围按位与

给你两个整数 left 和 right ，表示区间 [left, right] ，返回此区间内所有数字 按位与 的结果（包含 left 、right 端点）。


```
impl Solution {
    /// 核心思路：
    /// 区间 [left, right] 内所有数字按位与的结果，
    /// 等于 left 和 right 的二进制公共前缀，后面全部补 0。
    ///
    /// 因为只要某一位在 left 到 right 之间发生过变化（0 和 1 都出现过），
    /// 该位按位与的结果必然是 0；只有那些始终不变的高位才能保留下来。
    ///
    /// 实现上：找到 left 和 right 最高的不同位，
    /// 将该位及更低位全部清零，即保留公共前缀。
    pub fn range_bitwise_and(left: i32, right: i32) -> i32 {
        // left ^ right 的最高有效位位置 + 1，即需要清零的位数
        let shift = 32 - (left ^ right).leading_zeros();

        // 把最低 shift 位清零，保留公共前缀
        left & !((1i32 << shift) - 1)
    }
}
```

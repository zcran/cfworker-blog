---
title: "leetcode-记忆化21"
date: 2026-05-06T20:22:34+08:00
tags: ["leetcode", "记忆化"]
draft: false
---


## 使整数变为 0 的最少操作次数

给你一个整数 n，你需要重复执行多次下述操作将其转换为 0 ：

翻转 n 的二进制表示中最右侧位（第 0 位）。
如果二进制表示中的第 (i-1) 位为 1 且从第 (i-2) 位到第 0 位都为 0，则翻转 n 的二进制表示中的第 i 位。

返回将 n 转换为 0 的最小操作次数。

```
impl Solution {
    /// 将整数 n 变为 0 所需的最小“一位操作”次数。
    /// 操作定义：可以翻转二进制表示的最低位；或当低位连续为 1 时，翻转更高一位。
    /// 该递归解法基于公式：f(0)=0，对于 n>0，令 k = floor(log2(n))，则
    /// f(n) = 2^(k+1) - 1 - f(n - 2^k)。
    pub fn minimum_one_bit_operations(n: i32) -> i32 {
        // 递归基：0 不需要任何操作
        if n == 0 {
            return 0;
        }

        // 找出 n 的最高位位置 k
        // 使用整数方法 ilog2，避免浮点误差（原代码使用 f64::log2 也可行）
        let highest_bit_pos = n.ilog2(); // 返回类型 u32
        // 2^k 的值
        let power_of_two = 1 << highest_bit_pos;

        // 计算剩余部分 n' = n - 2^k
        let remainder = n - power_of_two;

        // 根据公式递归计算
        // 2^(k+1) - 1 可写作 (1 << (k+1)) - 1
        let total = (1 << (highest_bit_pos + 1)) - 1 - Self::minimum_one_bit_operations(remainder);

        total
    }
}
```

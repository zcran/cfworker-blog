---
title: "leetcode-递归26"
date: 2026-05-26T10:00:18+08:00
tags: ["leetcode", "递归"]
draft: false
---


## 统计好数字的数目


我们称一个数字字符串是 好数字 当它满足（下标从 0 开始）偶数 下标处的数字为 偶数 且 奇数 下标处的数字为 质数 （2，3，5 或 7）。

比方说，"2582" 是好数字，因为偶数下标处的数字（2 和 8）是偶数且奇数下标处的数字（5 和 2）为质数。但 "3245" 不是 好数字，因为 3 在偶数下标处但不是偶数。
给你一个整数 n ，请你返回长度为 n 且为好数字的数字字符串 总数 。由于答案可能会很大，请你将它对 10^9 + 7 取余后返回 。

一个 数字字符串 是每一位都由 0 到 9 组成的字符串，且可能包含前导 0 。


```
const MOD: i64 = 1_000_000_007;

impl Solution {
    /// 计算好数字的个数
    ///
    /// # 问题说明
    /// 好数字定义为：偶数索引位置（0-based）只能是偶数（0,2,4,6,8），
    /// 奇数索引位置只能是质数（2,3,5,7）
    ///
    /// # 数学原理
    /// - 偶数位置（ceil(n/2) 个）：每个位置有 5 种选择 (0,2,4,6,8)
    /// - 奇数位置（floor(n/2) 个）：每个位置有 4 种选择 (2,3,5,7)
    /// - 总组合数 = 5^(偶数位置数) × 4^(奇数位置数) mod MOD
    ///
    /// # 复杂度
    /// - 时间复杂度：O(log n) - 快速幂
    /// - 空间复杂度：O(1)
    ///
    /// # 示例
    /// ```
    /// assert_eq!(Solution::count_good_numbers(1), 5);  // [0,2,4,6,8]
    /// assert_eq!(Solution::count_good_numbers(4), 400); // 5^2 * 4^2 = 25 * 16 = 400
    /// ```
    pub fn count_good_numbers(n: i64) -> i32 {
        let even_positions = (n + 1) / 2;  // 偶数索引位置数量（0-based）
        let odd_positions = n / 2;          // 奇数索引位置数量

        let result = (Self::pow_mod(5, even_positions) * Self::pow_mod(4, odd_positions)) % MOD;
        result as i32
    }

    /// 快速幂取模
    ///
    /// 使用二进制指数分解法，计算 base^exp mod MOD
    ///
    /// # 参数
    /// - `base`: 底数
    /// - `mut exp`: 指数（非负）
    ///
    /// # 返回
    /// - base^exp mod MOD
    ///
    /// # 算法说明
    /// 将指数表示为二进制，例如 13 = 1101₂ = 8+4+1
    /// base^13 = base^8 × base^4 × base^1
    /// 通过反复平方可以高效计算
    #[inline]
    fn pow_mod(base: i32, mut exp: i64) -> i64 {
        let mut result = 1i64;
        let mut current = base as i64;

        while exp > 0 {
            // 如果当前二进制位为 1，乘入结果
            if exp & 1 == 1 {
                result = (result * current) % MOD;
            }
            // 底数平方，准备下一位
            current = (current * current) % MOD;
            // 右移一位，处理下一个二进制位
            exp >>= 1;
        }

        result
    }
}
```

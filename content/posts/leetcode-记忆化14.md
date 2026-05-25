---
title: "leetcode-记忆化14"
date: 2026-05-06T20:22:33+08:00
tags: ["leetcode", "记忆化"]
draft: false
---



## 表示数字的最少运算符

给定一个正整数 x，我们将会写出一个形如 x (op1) x (op2) x (op3) x ... 的表达式，其中每个运算符 op1，op2，… 可以是加、减、乘、除（+，-，*，或是 /）之一。例如，对于 x = 3，我们可以写出表达式 3 * 3 / 3 + 3 - 3，该式的值为 3 。

在写这样的表达式时，我们需要遵守下面的惯例：

· 除运算符（/）返回有理数。
· 任何地方都没有括号。
· 我们使用通常的操作顺序：乘法和除法发生在加法和减法之前。
· 不允许使用一元否定运算符（-）。例如，“x - x” 是一个有效的表达式，因为它只使用减法，但是 “-x + x” 不是，因为它使用了否定运算符。

我们希望编写一个能使表达式等于给定的目标值 target 且运算符最少的表达式。返回所用运算符的最少数量。


```
impl Solution {
    /// 计算最少运算符个数，使得表达式等于 target，运算符仅包含 +、-、*、/，
    /// 且操作数只能是 1、x、x^2、...（x 为给定的基数）。
    ///
    /// 算法：
    /// - 将 target 转换为 x 进制，得到从低位到高位的数字序列 operands。
    /// - 动态规划：dp[i][0] 表示处理到第 i 位（低位为 0）时，采用“加法”模式的最小操作数；
    ///   dp[i][1] 表示采用“减法”模式的最小操作数。
    /// - 转移时考虑当前位的数字以及进位/借位的影响。
    /// - 最终答案减 1（因为第一个数字不需要运算符）。
    pub fn least_ops_express_target(x: i32, target: i32) -> i32 {
        const ADD: usize = 0;
        const SUB: usize = 1;

        // 将 target 转换为 x 进制，低位在前
        let digits = Self::to_base_x(x, target);
        let n = digits.len();

        // dp[i][ADD/SUB]：处理 digits[i..] 的最小操作数
        let mut dp = vec![vec![0i32; 2]; n + 1];
        dp[n][ADD] = 0;
        dp[n][SUB] = i32::MAX;

        // 从最高位向低位递推
        for i in (0..n).rev() {
            let weight = if i > 0 { i as i32 } else { 2 }; // 每一位的运算符系数
            let dig = digits[i];

            // 加法分支：当前位用 dig 个 x^i 相加，后续取最优
            dp[i][ADD] = dp[i + 1][ADD].min(dp[i + 1][SUB]) + dig * weight;

            // 减法分支：用 (x - dig) 个 x^i 相减，并向高位借 1（借位代价为 i+1）
            let borrow = (i + 1) as i32; // 关键修正：保持与原 C++ 一致
            dp[i][SUB] = (dp[i + 1][ADD] + borrow)
                .min(dp[i + 1][SUB] - borrow)
                + (x - dig) * weight;
        }

        // 减去初始的 1（第一个操作数不计入运算符）
        dp[0][ADD].min(dp[0][SUB]) - 1
    }

    /// 将十进制 target 转换为以 x 为基底的数字序列（低位在前）
    fn to_base_x(x: i32, mut target: i32) -> Vec<i32> {
        let mut digits = Vec::new();
        while target > 0 {
            digits.push(target % x);
            target /= x;
        }
        digits
    }
}
```

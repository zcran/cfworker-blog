---
title: "leetcode-模拟14"
date: 2026-08-08T11:31:09+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 求解方程

求解一个给定的方程，将x以字符串 "x=#value" 的形式返回。该方程仅包含 '+' ， '-' 操作，变量 x 和其对应系数。

如果方程没有解或存在的解不为整数，请返回 "No solution" 。如果方程有无限解，则返回 “Infinite solutions” 。

题目保证，如果方程中只有一个解，则 'x' 的值是一个整数。


```
impl Solution {
    pub fn solve_equation(equation: String) -> String {
        // 将方程拆分为左右两边，分别解析 x 的系数和常数项
        let (left, right) = equation.split_once('=').unwrap();
        let (x_left, num_left) = Self::parse(left);
        let (x_right, num_right) = Self::parse(right);

        // 移项：将所有 x 项移到左边，常数项移到右边
        let x_coeff = x_left - x_right;      // ax = b 中的 a
        let constant = num_right - num_left; // ax = b 中的 b

        match x_coeff {
            0 if constant == 0 => "Infinite solutions".to_string(),
            0 => "No solution".to_string(),
            _ => format!("x={}", constant / x_coeff),
        }
    }

    /// 解析表达式，返回 (x 的系数总和, 常数项总和)
    fn parse(expr: &str) -> (i32, i32) {
        let bytes = expr.as_bytes();
        let n = bytes.len();
        let mut i = 0;
        let mut x_sum = 0;   // x 系数累计
        let mut num_sum = 0; // 常数项累计

        while i < n {
            // 确定当前项的符号（首项可省略正号）
            let mut sign = 1;
            if bytes[i] == b'+' {
                i += 1;
            } else if bytes[i] == b'-' {
                sign = -1;
                i += 1;
            }

            // 读取数字部分
            let mut val = 0;
            let start = i;
            while i < n && bytes[i].is_ascii_digit() {
                val = val * 10 + (bytes[i] - b'0') as i32;
                i += 1;
            }

            // 判断是 x 项还是常数项
            if i < n && bytes[i] == b'x' {
                // x 项：如果数字部分为空（如 "x" 或 "-x"），系数为 1
                let coeff = if i == start { 1 } else { val };
                x_sum += sign * coeff;
                i += 1; // 跳过 'x'
            } else {
                // 常数项
                num_sum += sign * val;
            }
        }

        (x_sum, num_sum)
    }
}
```

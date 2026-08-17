---
title: "leetcode-模拟13"
date: 2026-08-08T11:31:09+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 分数加减运算

给定一个表示分数加减运算的字符串 expression ，你需要返回一个字符串形式的计算结果。

这个结果应该是不可约分的分数，即 最简分数。 如果最终结果是一个整数，例如 2，你需要将它转换成分数形式，其分母为 1。所以在上述例子中, 2 应该被转换为 2/1。




```
impl Solution {
    pub fn fraction_addition(expression: String) -> String {
        // 当前累计分数，初始为 0/1
        let (mut num, mut den) = (0_i64, 1_i64);
        let bytes = expression.as_bytes();
        let n = bytes.len();
        let mut i = 0;

        while i < n {
            // 解析符号（第一个分数可能省略正号）
            let sign = if bytes[i] == b'+' {
                i += 1;
                1
            } else if bytes[i] == b'-' {
                i += 1;
                -1
            } else {
                1 // 第一个分数省略了正号
            };

            // 解析分子
            let mut curr_num = 0_i64;
            while i < n && bytes[i].is_ascii_digit() {
                curr_num = curr_num * 10 + (bytes[i] - b'0') as i64;
                i += 1;
            }
            curr_num *= sign; // 应用符号

            i += 1; // 跳过 '/'

            // 解析分母
            let mut curr_den = 0_i64;
            while i < n && bytes[i].is_ascii_digit() {
                curr_den = curr_den * 10 + (bytes[i] - b'0') as i64;
                i += 1;
            }

            // 分数相加: num/den + curr_num/curr_den
            num = num * curr_den + curr_num * den;
            den *= curr_den;

            // 立即约分，防止数值爆炸和溢出
            let g = gcd(num.abs(), den.abs());
            num /= g;
            den /= g;
        }

        // 确保分母为正（题目要求）
        if den < 0 {
            num = -num;
            den = -den;
        }

        format!("{}/{}", num, den)
    }
}

/// 迭代式欧几里得算法求最大公约数
fn gcd(mut a: i64, mut b: i64) -> i64 {
    while b != 0 {
        let tmp = a % b;
        a = b;
        b = tmp;
    }
    a
}
```

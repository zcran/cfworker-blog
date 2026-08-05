---
title: "leetcode-回溯32"
date: 2026-07-04T10:22:03+08:00
tags: ["leetcode", "回溯"]
draft: false
---


## 模糊坐标

我们有一些二维坐标，如 "(1, 3)" 或 "(2, 0.5)"，然后我们移除所有逗号，小数点和空格，得到一个字符串S。返回所有可能的原始字符串到一个列表中。

原始的坐标表示法不会存在多余的零，所以不会出现类似于"00", "0.0", "0.00", "1.0", "001", "00.01"或一些其他更小的数来表示坐标。此外，一个小数点前至少存在一个数，所以也不会出现“.1”形式的数字。

最后返回的列表可以是任意顺序的。而且注意返回的两个数字中间（逗号之后）都有一个空格。


```
impl Solution {
    /// 返回所有可能的原始坐标表示
    ///
    /// 思路：将字符串 s 去掉首尾括号后，在中间位置分割成两部分，
    /// 每部分分别生成所有可能的合法数字表示（整数或小数），然后组合
    pub fn ambiguous_coordinates(s: String) -> Vec<String> {
        let digits = &s[1..s.len() - 1]; // 去掉首尾的 '(' 和 ')'
        let mut result = Vec::new();

        /// 生成字符串 `s` 所有可能的合法数字表示
        /// 返回：原始整数（如果合法）和所有合法的小数表示
        fn possible_numbers(s: &str) -> Vec<String> {
            let mut res = Vec::new();
            let n = s.len();

            // 特殊情况：空字符串
            if n == 0 {
                return res;
            }

            // 1. 整体作为整数（必须没有前导零，除非是单个 "0"）
            if n == 1 || !s.starts_with('0') {
                res.push(s.to_string());
            }

            // 2. 插入小数点，生成小数
            // 规则：整数部分不能有前导零（除非是单个 "0"），小数部分不能以零结尾
            for i in 1..n {
                let int_part = &s[0..i];
                let dec_part = &s[i..n];

                // 整数部分合法：长度为1 或 不以 '0' 开头
                // 小数部分合法：不以 '0' 结尾
                if (i == 1 || !int_part.starts_with('0')) && !dec_part.ends_with('0') {
                    res.push(format!("{}.{}", int_part, dec_part));
                }
            }

            res
        }

        // 在中间位置分割：左边至少1位，右边至少1位
        for i in 1..digits.len() {
            let left = &digits[0..i];
            let right = &digits[i..];

            let left_nums = possible_numbers(left);
            let right_nums = possible_numbers(right);

            // 组合所有可能性
            for l in &left_nums {
                for r in &right_nums {
                    result.push(format!("({}, {})", l, r));
                }
            }
        }

        result
    }
}
```

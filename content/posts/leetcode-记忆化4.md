---
title: "leetcode-记忆化4"
date: 2026-05-06T20:22:33+08:00
tags: ["leetcode", "记忆化"]
draft: false
---



## 为运算表达式设计优先级

给你一个由数字和运算符组成的字符串 expression ，按不同优先级组合数字和运算符，计算并返回所有可能组合的结果。你可以 按任意顺序 返回答案。

生成的测试用例满足其对应输出值符合 32 位整数范围，不同结果的数量不超过 104 。

```
impl Solution {
    /// 给定一个由数字和运算符 `+`、`-`、`*` 组成的表达式，
    /// 返回所有可能通过添加括号改变运算顺序所得到的计算结果。
    ///
    /// 使用分治法（Divide and Conquer）：
    /// 遇到运算符时，将其分为左右子表达式，分别递归计算所有可能的结果，
    /// 然后组合左右结果进行对应运算。
    ///
    /// 若当前子表达式无运算符，则直接解析为数字。
    pub fn diff_ways_to_compute(expression: String) -> Vec<i32> {
        // 辅助递归函数：对字符串切片计算所有可能结果
        fn compute(s: &str) -> Vec<i32> {
            let mut results = Vec::new();
            let bytes = s.as_bytes();

            // 遍历每个字符，寻找运算符
            for i in 0..bytes.len() {
                let op = bytes[i];
                // 当遇到运算符时，分裂表达式并进行递归
                if op == b'+' || op == b'-' || op == b'*' {
                    let left_results = compute(&s[..i]);
                    let right_results = compute(&s[i + 1..]);

                    // 函数式组合：对左右结果进行笛卡尔积，根据运算符计算结果
                    results.extend(
                        left_results
                            .iter()
                            .flat_map(|&l| right_results.iter().map(move |&r| apply_operator(l, r, op)))
                    );
                }
            }

            // 若未找到任何运算符，说明当前子串是纯数字，直接解析
            if results.is_empty() {
                results.push(s.parse().unwrap());
            }
            results
        }

        /// 根据运算符执行二元运算
        fn apply_operator(l: i32, r: i32, op: u8) -> i32 {
            match op {
                b'+' => l + r,
                b'-' => l - r,
                b'*' => l * r,
                _ => unreachable!("仅支持 + - * 运算符"),
            }
        }

        compute(&expression)
    }
}
```

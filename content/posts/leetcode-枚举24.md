---
title: "leetcode-枚举24"
date: 2026-07-09T10:05:00+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 向表达式添加括号后的最小结果


给你一个下标从 0 开始的字符串 expression ，格式为 "<num1>+<num2>" ，其中 <num1> 和 <num2> 表示正整数。

请你向 expression 中添加一对括号，使得在添加之后， expression 仍然是一个有效的数学表达式，并且计算后可以得到 最小 可能值。左括号 必须 添加在 '+' 的左侧，而右括号必须添加在 '+' 的右侧。

返回添加一对括号后形成的表达式 expression ，且满足 expression 计算得到 最小 可能值。如果存在多个答案都能产生相同结果，返回任意一个答案。

生成的输入满足：expression 的原始值和添加满足要求的任一对括号之后 expression 的值，都符合 32-bit 带符号整数范围。

```
impl Solution {
    /// 在表达式 "<num1>+<num2>" 中添加一对括号，使得结果最小。
    /// 左括号必须在 '+' 左侧，右括号必须在 '+' 右侧。
    /// 返回添加括号后的表达式。
    pub fn minimize_result(expression: String) -> String {
        // 找到 '+' 的位置
        let plus_idx = expression.find('+').unwrap();
        let chars: Vec<char> = expression.chars().collect();
        let n = chars.len();

        let mut min_value = i32::MAX;
        let mut best_expr = String::new();

        // 枚举左括号位置：在 '+' 左侧，即 0..plus_idx
        for left in 0..plus_idx {
            // 枚举右括号位置：在 '+' 右侧，即 plus_idx+1..n
            for right in plus_idx + 1..n {
                // 解析四个部分：A (B + C) D
                // A: expression[0..left]，可能为空，视为 1
                let a = if left == 0 {
                    1
                } else {
                    chars[0..left].iter().collect::<String>().parse::<i32>().unwrap()
                };

                // B: expression[left..plus_idx]
                let b = chars[left..plus_idx]
                    .iter()
                    .collect::<String>()
                    .parse::<i32>()
                    .unwrap();

                // C: expression[plus_idx+1..=right]
                let c = chars[plus_idx + 1..=right]
                    .iter()
                    .collect::<String>()
                    .parse::<i32>()
                    .unwrap();

                // D: expression[right+1..n]，可能为空，视为 1
                let d = if right == n - 1 {
                    1
                } else {
                    chars[right + 1..n]
                        .iter()
                        .collect::<String>()
                        .parse::<i32>()
                        .unwrap()
                };

                // 计算结果：A * (B + C) * D
                let value = a * (b + c) * d;

                // 更新最优解
                if value < min_value {
                    min_value = value;
                    best_expr = format!(
                        "{}({}){}",
                        &expression[0..left],
                        &expression[left..=right],
                        &expression[right + 1..]
                    );
                }
            }
        }

        best_expr
    }
}
```

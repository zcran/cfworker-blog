---
title: "leetcode-回溯21"
date: 2026-07-04T10:22:02+08:00
tags: ["leetcode", "回溯"]
draft: false
---


## 给表达式添加运算符

给定一个仅包含数字 0-9 的字符串 num 和一个目标值整数 target ，在 num 的数字之间添加 二元 运算符（不是一元）+、- 或 * ，返回 所有 能够得到 target 的表达式。

注意，返回表达式中的操作数 不应该 包含前导零。

注意，一个数字可以包含多个数位。


```
impl Solution {
    pub fn add_operators(num: String, target: i32) -> Vec<String> {
        let mut result = vec![];
        // 开始深度优先搜索，初始表达式为空字符串
        Self::dfs(
            &num,                // 数字字符串
            &mut result,         // 存储所有有效表达式
            "".to_owned(),       // 当前构建的表达式
            0,                   // 当前处理的索引位置
            target as i64,       // 目标值
            0,                   // 前一个操作数（用于乘法运算）
            0,                   // 当前表达式的计算结果
        );
        result
    }

    /// 深度优先搜索构建所有可能的表达式
    ///
    /// # 参数
    /// - `num`: 原始数字字符串
    /// - `ans`: 存储所有有效表达式的容器
    /// - `selected`: 当前已经构建的表达式字符串
    /// - `index`: 当前处理到的位置（从0开始）
    /// - `target`: 目标值
    /// - `prev`: 前一个操作数的值（用于乘法运算的回退）
    /// - `cur`: 当前表达式的计算结果
    pub fn dfs(
        num: &str,
        ans: &mut Vec<String>,
        selected: String,
        index: usize,
        target: i64,
        prev: i64,
        cur: i64,
    ) {
        // 基础情况：处理完所有数字
        if index == num.len() {
            // 如果当前计算结果等于目标值，则找到一个有效表达式
            if cur == target {
                ans.push(selected);
            }
            return;
        }

        let num_bytes = num.as_bytes();

        // 尝试从当前位置取不同长度的数字作为操作数
        for i in index..num.len() {
            // 剪枝：如果当前数字以 0 开头且长度大于 1，则跳过
            // 因为操作数不能包含前导零（如 "01" 非法）
            if i != index && num_bytes[index] == b'0' {
                break;
            }

            // 提取当前操作数 [index..=i]
            let val = (&num[index..=i]).parse::<i64>().unwrap();

            // 处理第一个数字：直接作为表达式的开头，不添加运算符
            if index == 0 {
                Self::dfs(
                    num,
                    ans,
                    val.to_string(),  // 表达式就是当前数字
                    i + 1,            // 从下一个位置继续
                    target,
                    val,              // 前一个操作数就是当前数字
                    val,              // 计算结果也是当前数字
                );
            } else {
                // 情况1：添加 '+' 运算符
                Self::dfs(
                    num,
                    ans,
                    format!("{}+{}", selected.clone(), val),  // 表达式: 原表达式 + 新数字
                    i + 1,
                    target,
                    val,                // 前一个操作数变为新数字
                    cur + val,          // 计算结果加上新数字
                );

                // 情况2：添加 '-' 运算符
                Self::dfs(
                    num,
                    ans,
                    format!("{}-{}", selected.clone(), val),  // 表达式: 原表达式 - 新数字
                    i + 1,
                    target,
                    -val,               // 前一个操作数变为负的新数字
                    cur - val,          // 计算结果减去新数字
                );

                // 情况3：添加 '*' 运算符
                Self::dfs(
                    num,
                    ans,
                    format!("{}*{}", selected.clone(), val),  // 表达式: 原表达式 * 新数字
                    i + 1,
                    target,
                    prev * val,         // 前一个操作数乘以新数字
                    cur - prev + prev * val,  // 回退重新计算：减去之前加上的 prev，加上 prev * val
                );
            }
        }
    }
}
```

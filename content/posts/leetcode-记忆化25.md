---
title: "leetcode-记忆化25"
date: 2026-05-06T20:22:34+08:00
tags: ["leetcode", "记忆化"]
draft: false
---


## 解出数学表达式的学生分数

给你一个字符串 s ，它 只 包含数字 0-9 ，加法运算符 '+' 和乘法运算符 '*' ，这个字符串表示一个 合法 的只含有 个位数数字 的数学表达式（比方说 3+5*2）。有 n 位小学生将计算这个数学表达式，并遵循如下 运算顺序 ：

1.按照 从左到右 的顺序计算 乘法 ，然后
2.按照 从左到右 的顺序计算 加法 。

给你一个长度为 n 的整数数组 answers ，表示每位学生提交的答案。你的任务是给 answer 数组按照如下 规则 打分：

· 如果一位学生的答案 等于 表达式的正确结果，这位学生将得到 5 分。
· 否则，如果答案由 一处或多处错误的运算顺序 计算得到，那么这位学生能得到 2 分。
· 否则，这位学生将得到 0 分。

请你返回所有学生的分数和。

```
use std::collections::HashSet;

impl Solution {
    /// 计算学生们的总得分（LeetCode 2019）
    /// 规则：
    /// - 表达式只包含数字 1-9 和运算符 '+'、'*'，正常运算顺序遵循先乘后加。
    /// - 学生可以任意加括号改变运算顺序，得到不同的可能结果。
    /// - 如果学生答案等于正确结果，得 5 分；如果等于其他可能结果，得 2 分；否则 0 分。
    /// 返回所有学生的总得分。
    pub fn score_of_students(s: String, answers: Vec<i32>) -> i32 {
        // ---------- Step 1: 统计每个答案的出现次数 ----------
        // 答案范围在 [0, 1000] 内，使用固定大小数组效率更高
        let mut count = vec![0; 1001];
        for &ans in &answers {
            if ans <= 1000 {
                count[ans as usize] += 1;
            }
        }

        // ---------- Step 2: 计算正确结果（严格遵循先乘后加） ----------
        let correct = Self::compute_correct(&s);

        // ---------- Step 3: 动态规划，枚举所有可能的计算结果 ----------
        let all_possible = Self::enumerate_possible_results(&s);

        // ---------- Step 4: 统计得分 ----------
        let mut score = 5 * count[correct as usize]; // 正确答案每人 5 分
        for &val in &all_possible {
            if val != correct {
                score += 2 * count[val as usize];
            }
        }
        score
    }

    /// 计算表达式严格按先乘后加的正确值
    /// 使用栈模拟：遇到乘号立即与栈顶相乘，遇到加号则入栈，最后累加栈中所有数
    fn compute_correct(s: &str) -> i32 {
        let chars: Vec<char> = s.chars().collect();
        let mut stack = Vec::new();
        // 第一个数字入栈
        stack.push(chars[0].to_digit(10).unwrap() as i32);
        // 跳过运算符（索引 1,3,5...）和数字（索引 2,4,6...）
        let mut i = 1;
        while i < chars.len() {
            let op = chars[i];
            let num = chars[i + 1].to_digit(10).unwrap() as i32;
            if op == '*' {
                // 乘法：与栈顶元素相乘
                let top = stack.pop().unwrap();
                stack.push(top * num);
            } else {
                // 加法：直接入栈
                stack.push(num);
            }
            i += 2;
        }
        // 栈中所有元素求和（都是加法结果）
        stack.into_iter().sum()
    }

    /// 动态规划枚举所有可能的计算结果（学生任意加括号时得到的值）
    /// 返回一个 HashSet，包含所有可能的值（≤1000）
    fn enumerate_possible_results(s: &str) -> HashSet<i32> {
        let chars: Vec<char> = s.chars().collect();
        let len = chars.len();
        // dp[i][j] 表示子串 s[i..=j] 所有可能的计算结果，i 和 j 均为偶数（数字位置）
        // 多分配两行两列，避免索引越界
        let mut dp = (0..len + 2)
            .map(|_| (0..len + 2).map(|_| HashSet::new()).collect())
            .collect::<Vec<Vec<HashSet<i32>>>>();

        // 初始化：单个数字
        for i in (0..len).step_by(2) {
            dp[i][i].insert(chars[i].to_digit(10).unwrap() as i32);
        }

        // 枚举子串长度（步长），从一个数字到两个数字、三个数字...
        for step in (2..len).step_by(2) {
            for i in (0..len - step).step_by(2) {
                let j = i + step;
                let mut possible = HashSet::new(); // 临时存储当前子串的所有结果

                // 枚举左半部分长度
                for t in (0..step).step_by(2) {
                    let left_end = i + t;
                    let right_start = i + t + 2; // 跳过中间的运算符
                    let op = chars[i + t + 1];

                    // 不可变借用左右子集
                    let left_set = &dp[i][left_end];
                    let right_set = &dp[right_start][j];

                    for &x in left_set {
                        for &y in right_set {
                            let res = if op == '+' { x + y } else { x * y };
                            if res <= 1000 {
                                possible.insert(res);
                            }
                        }
                    }
                }
                // 将计算结果赋值给 dp[i][j]（覆盖原来的空集）
                dp[i][j] = possible;
            }
        }

        // 返回整个表达式的所有可能结果
        dp[0][len - 1].clone()
    }
}
```

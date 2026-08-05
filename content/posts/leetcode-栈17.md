---
title: "leetcode-栈17"
date: 2026-07-24T10:16:58+08:00
tags: ["leetcode", "栈"]
draft: false
---


## 棒球比赛

你现在是一场采用特殊赛制棒球比赛的记录员。这场比赛由若干回合组成，过去几回合的得分可能会影响以后几回合的得分。

比赛开始时，记录是空白的。你会得到一个记录操作的字符串列表 ops，其中 ops[i] 是你需要记录的第 i 项操作，ops 遵循下述规则：

1. 整数 x - 表示本回合新获得分数 x
2. "+" - 表示本回合新获得的得分是前两次得分的总和。题目数据保证记录此操作时前面总是存在两个有效的分数。
3. "D" - 表示本回合新获得的得分是前一次得分的两倍。题目数据保证记录此操作时前面总是存在一个有效的分数。
4. "C" - 表示前一次得分无效，将其从记录中移除。题目数据保证记录此操作时前面总是存在一个有效的分数。

请你返回记录中所有得分的总和。


```
// 棒球比赛计分器
// 使用栈记录有效分数，支持整数、D（双倍）、C（撤销）、+（前两分之和）
impl Solution {
    pub fn cal_points(operations: Vec<String>) -> i32 {
        let mut scores = Vec::with_capacity(operations.len());

        for op in operations {
            match op.as_bytes()[0] {
                b'+' => {
                    let len = scores.len();
                    scores.push(scores[len - 2] + scores[len - 1]);
                }
                b'D' => {
                    let last = *scores.last().unwrap();
                    scores.push(last * 2);
                }
                b'C' => {
                    scores.pop();
                }
                _ => {
                    scores.push(op.parse::<i32>().unwrap());
                }
            }
        }

        scores.iter().sum()
    }
}
```

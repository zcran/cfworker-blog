---
title: "leetcode-堆51"
date: 2026-08-21T09:54:13+08:00
tags: ["leetcode", "堆"]
draft: false
---


## 奖励最顶尖的 K 名学生

给你两个字符串数组 positive_feedback 和 negative_feedback ，分别包含表示正面的和负面的词汇。不会 有单词同时是正面的和负面的。

一开始，每位学生分数为 0 。每个正面的单词会给学生的分数 加 3 分，每个负面的词会给学生的分数 减  1 分。

给你 n 个学生的评语，用一个下标从 0 开始的字符串数组 report 和一个下标从 0 开始的整数数组 student_id 表示，其中 student_id[i] 表示这名学生的 ID ，这名学生的评语是 report[i] 。每名学生的 ID 互不相同。

给你一个整数 k ，请你返回按照得分 从高到低 最顶尖的 k 名学生。如果有多名学生分数相同，ID 越小排名越前。


```
use std::collections::HashSet;

impl Solution {
    /// 返回得分最高的 k 名学生。
    ///
    /// 计分规则：
    /// - 正面词汇 +3 分
    /// - 负面词汇 -1 分
    ///
    /// 排序规则：
    /// - 分数降序
    /// - 分数相同则 ID 升序（ID 小的排前面）
    pub fn top_students(
        positive_feedback: Vec<String>,
        negative_feedback: Vec<String>,
        report: Vec<String>,
        student_id: Vec<i32>,
        k: i32,
    ) -> Vec<i32> {
        // 将正/负面词汇放入 HashSet，实现 O(1) 查询
        let positive: HashSet<&str> = positive_feedback.iter().map(|s| s.as_str()).collect();
        let negative: HashSet<&str> = negative_feedback.iter().map(|s| s.as_str()).collect();

        // 并行遍历 student_id 和 report，计算每个学生的得分
        let mut scores: Vec<(i32, i32)> = student_id
            .into_iter()
            .zip(report.into_iter())
            .map(|(id, report)| {
                let score = report
                    .split_whitespace()
                    .map(|word| {
                        if positive.contains(word) {
                            3
                        } else if negative.contains(word) {
                            -1
                        } else {
                            0
                        }
                    })
                    .sum();
                (id, score)
            })
            .collect();

        // 按分数降序、ID 升序排序
        scores.sort_by(|a, b| b.1.cmp(&a.1).then_with(|| a.0.cmp(&b.0)));

        // 取前 k 个学生的 ID
        scores.into_iter().map(|(id, _)| id).take(k as usize).collect()
    }
}
```

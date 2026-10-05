---
title: "leetcode-贪心189"
date: 2026-10-03T12:55:46+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 完成一半题目

有 N 位扣友参加了微软与力扣举办了「以扣会友」线下活动。主办方提供了 2*N 道题目，整型数组 questions 中每个数字对应了每道题目所涉及的知识点类型。 若每位扣友选择不同的一题，请返回被选的 N 道题目至少包含多少种知识点类型。


```
use std::collections::HashMap;

impl Solution {
    pub fn half_questions(questions: Vec<i32>) -> i32 {
        // 要选 n = len/2 道题，且知识点种类尽可能少，
        // 所以优先选出现次数最多的知识点类型。
        // 统计各类型出现次数，降序累加，直到 >= n 为止。
        let n = questions.len() / 2;

        let mut count: HashMap<i32, usize> = HashMap::new();
        for q in questions {
            *count.entry(q).or_insert(0) += 1;
        }

        let mut freq: Vec<usize> = count.into_values().collect();
        freq.sort_unstable_by(|a, b| b.cmp(a)); // 降序

        let mut chosen = 0;
        let mut kinds = 0;
        for f in freq {
            chosen += f;
            kinds += 1;
            if chosen >= n {
                break;
            }
        }
        kinds
    }
}
```

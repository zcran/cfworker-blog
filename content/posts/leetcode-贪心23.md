---
title: "leetcode-贪心23"
date: 2026-10-03T12:55:35+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 森林中的兔子

森林中有未知数量的兔子。提问其中若干只兔子 "还有多少只其它兔子与你（指被提问的兔子）颜色相同?" ，将答案收集到一个整数数组 answers 中，其中 answers[i] 是第 i 只兔子的回答。

给你数组 answers ，返回森林中兔子的最少数量。


```
use std::collections::HashMap;

impl Solution {
    pub fn num_rabbits(answers: Vec<i32>) -> i32 {
        // 按回答值分组统计：回答 y 的兔子，说明同色组最多有 y+1 只
        // 每组至少需要 ceil(count / (y+1)) * (y+1) 只兔子
        let mut count: HashMap<i32, i32> = HashMap::new();
        for y in answers {
            *count.entry(y).or_insert(0) += 1;
        }

        count
            .into_iter()
            .map(|(y, x)| (x + y) / (y + 1) * (y + 1))
            .sum()
    }
}
```

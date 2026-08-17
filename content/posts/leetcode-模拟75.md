---
title: "leetcode-模拟75"
date: 2026-08-08T11:31:13+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 任务调度器 II

给你一个下标从 0 开始的正整数数组 tasks ，表示需要 按顺序 完成的任务，其中 tasks[i] 表示第 i 件任务的 类型 。

同时给你一个正整数 space ，表示一个任务完成 后 ，另一个 相同 类型任务完成前需要间隔的 最少 天数。

在所有任务完成前的每一天，你都必须进行以下两种操作中的一种：

完成 tasks 中的下一个任务

休息一天

请你返回完成所有任务所需的 最少 天数。


```
use std::collections::HashMap;

impl Solution {
    /// 模拟任务调度：每个任务类型完成后，需间隔 space 天才能再做同类型。
    /// 用 HashMap 记录每种任务下一次可执行的最早天数。
    pub fn task_scheduler_ii(tasks: Vec<i32>, space: i32) -> i64 {
        let mut day = 0i64;
        let mut next_available: HashMap<i32, i64> = HashMap::new();
        let gap = space as i64;

        for task in tasks {
            // 如果该任务类型有冷却限制，跳到最早可执行的那天
            if let Some(&earliest) = next_available.get(&task) {
                day = day.max(earliest);
            }
            // 完成当前任务，天数 +1
            day += 1;
            // 更新该任务类型的下次最早可执行时间
            next_available.insert(task, day + gap);
        }

        day
    }
}
```

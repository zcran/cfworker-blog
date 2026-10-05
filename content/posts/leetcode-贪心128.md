---
title: "leetcode-贪心128"
date: 2026-10-03T12:55:42+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 最小处理时间

你有 n 颗处理器，每颗处理器都有 4 个核心。现有 n * 4 个待执行任务，每个核心只执行 一次 任务。

给你一个下标从 0 开始的整数数组 processorTime ，表示每颗处理器最早空闲时间。另给你一个下标从 0 开始的整数数组 tasks ，表示执行每个任务所需的时间。返回所有任务都执行完毕需要的 最小时间 。

注意：每个核心独立执行任务。




```
impl Solution {
    pub fn min_processing_time(mut processor_time: Vec<i32>, mut tasks: Vec<i32>) -> i32 {
        // 每颗处理器有 4 个核心，负责 4 个任务；瓶颈由该处理器上最耗时的任务决定。
        // 贪心：最早空闲的处理器配最耗时的 4 个任务，最晚空闲的处理器配最省时的 4 个任务，
        // 这样能让"空闲时间 + 任务耗时"的最大值最小。
        processor_time.sort_unstable();
        tasks.sort_unstable_by(|a, b| b.cmp(a)); // 任务降序

        processor_time
            .iter()
            .enumerate()
            .map(|(i, &p)| p + tasks[i * 4])
            .max()
            .unwrap()
    }
}
```

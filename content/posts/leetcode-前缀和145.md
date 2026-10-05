---
title: "leetcode-前缀和145"
date: 2026-08-25T10:02:55+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 统计每个班次结束后的未完成任务数

给你两个整数数组 tasks 和 shifts。

· tasks[i] 表示完成第 i^th 个任务所需的时间。
· shifts[j] 表示第 j^th 个班次可用的时间。

任务 必须 按照从左到右的顺序处理。

· 延续处理：如果一个任务在当前班次内没有完成，则下一班次会从该任务的 相同进度位置 继续处理。
· 重新开始：如果一个班次内完成了所有任务，则该班次会结束 。该班次剩余的时间会被 丢弃，下一班次会重新从第 0 个任务开始。

如果一个任务尚未被完全完成，则认为该任务是 未完成 的。这包括当前正在执行中的任务。

返回一个整数数组 ans，其中 ans[j] 表示第 j^th 个班次结束后剩余的 未完成 任务数量。


```
impl Solution {
    pub fn count_tasks(tasks: Vec<i32>, shifts: Vec<i32>) -> Vec<i32> {
        let n = tasks.len();

        // 前缀和：prefix[i] 表示前 i 个任务的总时间
        let mut prefix = vec![0i64; n];
        prefix[0] = tasks[0] as i64;
        for i in 1..n {
            prefix[i] = prefix[i - 1] + tasks[i] as i64;
        }
        let total_time = prefix[n - 1];

        let mut ans = Vec::with_capacity(shifts.len());
        let mut current_time = 0i64; // 当前班次已消耗的总时间（从上次重置开始）

        for &shift in &shifts {
            current_time += shift as i64;

            if current_time >= total_time {
                // 完成所有任务，重置状态
                current_time = 0;
                ans.push(0);
            } else {
                // 找到在 current_time 时间内能完成多少个任务
                // upper_bound 返回第一个 > current_time 的位置
                let completed = prefix.partition_point(|&t| t <= current_time);
                let remaining = n - completed;
                ans.push(remaining as i32);
            }
        }

        ans
    }
}
```

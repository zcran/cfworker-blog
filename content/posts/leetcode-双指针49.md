---
title: "leetcode-双指针49"
date: 2026-09-05T09:53:03+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 安排工作以达到最大收益

你有 n 个工作和 m 个工人。给定三个数组： difficulty, profit 和 worker ，其中:

difficulty[i] 表示第 i 个工作的难度，profit[i] 表示第 i 个工作的收益。
worker[i] 是第 i 个工人的能力，即该工人只能完成难度小于等于 worker[i] 的工作。

每个工人 最多 只能安排 一个 工作，但是一个工作可以 完成多次 。

举个例子，如果 3 个工人都尝试完成一份报酬为 $1 的同样工作，那么总收益为 $3 。如果一个工人不能完成任何工作，他的收益为 $0 。

返回 在把工人分配到工作岗位后，我们所能获得的最大利润 。


```
impl Solution {
    pub fn max_profit_assignment(difficulty: Vec<i32>, profit: Vec<i32>, mut worker: Vec<i32>) -> i32 {
        // 将工作按难度排序
        let mut jobs: Vec<(i32, i32)> = difficulty.into_iter().zip(profit).collect();
        jobs.sort_unstable_by_key(|&(d, _)| d);

        // 工人按能力排序
        worker.sort_unstable();

        let mut total_profit = 0;
        let mut max_profit = 0;
        let mut job_idx = 0;

        for &ability in &worker {
            // 当前工人能完成的所有工作中，取最大收益
            while job_idx < jobs.len() && jobs[job_idx].0 <= ability {
                max_profit = max_profit.max(jobs[job_idx].1);
                job_idx += 1;
            }
            total_profit += max_profit;
        }

        total_profit
    }
}
```

---
title: "leetcode-计数63"
date: 2026-08-01T10:40:54+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 完成所有任务需要的最少轮数

给你一个下标从 0 开始的整数数组 tasks ，其中 tasks[i] 表示任务的难度级别。在每一轮中，你可以完成 2 个或者 3 个 相同难度级别 的任务。

返回完成所有任务需要的 最少 轮数，如果无法完成所有任务，返回 -1 。


```
use std::collections::HashMap;

impl Solution {
    pub fn minimum_rounds(tasks: Vec<i32>) -> i32 {
        // 统计每个难度级别的任务数量
        let mut count = HashMap::with_capacity(tasks.len());
        for task in tasks {
            *count.entry(task).or_insert(0) += 1;
        }

        let mut rounds = 0;

        for &c in count.values() {
            // 如果某个难度只有1个任务，无法完成
            if c == 1 {
                return -1;
            }

            // 每轮可以完成2或3个任务
            // 最优策略：尽可能用3个一组，剩余的用2个一组
            rounds += c / 3;
            if c % 3 != 0 {
                rounds += 1;
            }
        }

        rounds
    }
}
```

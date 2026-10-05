---
title: "leetcode-贪心63"
date: 2026-10-03T12:55:38+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 完成所有任务的最少初始能量

给你一个任务数组 tasks ，其中 tasks[i] = [actuali, minimumi] ：

actuali 是完成第 i 个任务 需要耗费 的实际能量。
minimumi 是开始第 i 个任务前需要达到的最低能量。

比方说，如果任务为 [10, 12] 且你当前的能量为 11 ，那么你不能开始这个任务。如果你当前的能量为 13 ，你可以完成这个任务，且完成它后剩余能量为 3 。

你可以按照 任意顺序 完成任务。

请你返回完成所有任务的 最少 初始能量。


```
impl Solution {
    pub fn minimum_effort(mut tasks: Vec<Vec<i32>>) -> i32 {
        // 按 (minimum - actual) 降序排序：净消耗大的任务先做，初始能量需求最低
        tasks.sort_unstable_by(|a, b| (b[1] - b[0]).cmp(&(a[1] - a[0])));

        // 从后往前累加：need 表示完成当前及之后所有任务所需的最小初始能量
        let mut need = 0;
        for t in tasks.iter().rev() {
            // 进入当前任务前需满足 minimum，且完成后剩余能量要 ≥ need
            need = (need + t[0]).max(t[1]);
        }
        need
    }
}
```

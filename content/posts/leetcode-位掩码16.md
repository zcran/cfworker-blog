---
title: "leetcode-位掩码16"
date: 2026-05-18T10:27:59+08:00
tags: ["leetcode", "位掩码"]
draft: false
---


## 完成所有工作的最短时间


给你一个整数数组 jobs ，其中 jobs[i] 是完成第 i 项工作要花费的时间。

请你将这些工作分配给 k 位工人。所有工作都应该分配给工人，且每项工作只能分配给一位工人。工人的 工作时间 是完成分配给他们的所有工作花费时间的总和。请你设计一套最佳的工作分配方案，使工人的 最大工作时间 得以 最小化 。

返回分配方案中尽可能 最小 的 最大工作时间 。

```
impl Solution {
    pub fn minimum_time_required(jobs: Vec<i32>, k: i32) -> i32 {
        let k = k as usize;
        let total: i32 = jobs.iter().sum();
        let max_job = *jobs.iter().max().unwrap();

        // 二分答案：下界为最大任务，上界为总时间（开区间）
        let (mut low, mut high) = (max_job, total + 1);
        while low < high {
            let limit = (low + high) / 2;
            if can_assign(&jobs, k, limit) {
                high = limit;
            } else {
                low = limit + 1;
            }
        }
        low
    }
}

/// 判断能否将 jobs 分配到 k 个工人，每人总耗时不超过 limit
fn can_assign(jobs: &[i32], k: usize, limit: i32) -> bool {
    let mut workloads = vec![0; k];
    backtrack(jobs, 0, &mut workloads, limit)
}

/// 回溯分配每个任务
fn backtrack(jobs: &[i32], idx: usize, workloads: &mut [i32], limit: i32) -> bool {
    if idx == jobs.len() {
        return true;
    }
    let job = jobs[idx];
    // 尝试将当前任务分配给每个工人
    for i in 0..workloads.len() {
        // 剪枝1：超出容量
        if workloads[i] + job > limit {
            continue;
        }
        // 剪枝2：如果当前工人与之前某个工人负载相同，则跳过（避免重复状态）
        let is_dup = workloads[0..i].iter().any(|&w| w == workloads[i]);
        if is_dup {
            continue;
        }
        workloads[i] += job;
        if backtrack(jobs, idx + 1, workloads, limit) {
            return true;
        }
        workloads[i] -= job;
        // 剪枝3：如果当前工人负载为0，则后续分配必然失败（因为工人顺序无关）
        if workloads[i] == 0 {
            return false;
        }
    }
    false
}
```

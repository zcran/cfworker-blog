---
title: "leetcode-堆32"
date: 2026-08-21T09:54:12+08:00
tags: ["leetcode", "堆"]
draft: false
---


## 单线程 CPU

给你一个二维数组 tasks ，用于表示 n 项从 0 到 n - 1 编号的任务。其中 tasks[i] = [enqueueTimeˇi, processingTimeˇi] 意味着第 i 项任务将会于 enqueueTimeˇi 时进入任务队列，需要 processingTimeˇi 的时长完成执行。

现有一个单线程 CPU ，同一时间只能执行 最多一项 任务，该 CPU 将会按照下述方式运行：

如果 CPU 空闲，且任务队列中没有需要执行的任务，则 CPU 保持空闲状态。
如果 CPU 空闲，但任务队列中有需要执行的任务，则 CPU 将会选择 执行时间最短 的任务开始执行。如果多个任务具有同样的最短执行时间，则选择下标最小的任务开始执行。
一旦某项任务开始执行，CPU 在 执行完整个任务 前都不会停止。
CPU 可以在完成一项任务后，立即开始执行一项新任务。

返回 CPU 处理任务的顺序。


```
use std::collections::BinaryHeap;
use std::cmp::Reverse;

impl Solution {
    pub fn get_order(tasks: Vec<Vec<i32>>) -> Vec<i32> {
        let n = tasks.len();
        // (original_id, enqueue_time, processing_time)
        let mut jobs: Vec<(usize, i32, i32)> = tasks.into_iter()
            .enumerate()
            .map(|(i, t)| (i, t[0], t[1]))
            .collect();
        jobs.sort_by_key(|&(_, enq, _)| enq);

        let mut heap: BinaryHeap<Reverse<(i32, usize)>> = BinaryHeap::new();
        let mut ans: Vec<i32> = Vec::with_capacity(n);
        let mut time = 0;
        let mut idx = 0;

        while idx < n || !heap.is_empty() {
            // 修正：只有当 CPU 空闲且时间早于下一个任务时，才跳转到该任务入队时间
            if heap.is_empty() {
                time = time.max(jobs[idx].1);
            }

            // Add all tasks that have arrived by current time
            while idx < n && jobs[idx].1 <= time {
                heap.push(Reverse((jobs[idx].2, jobs[idx].0)));
                idx += 1;
            }

            // Execute the shortest task
            if let Some(Reverse((proc, id))) = heap.pop() {
                ans.push(id as i32);
                time += proc;
            }
        }

        ans
    }
}
```

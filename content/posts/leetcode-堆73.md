---
title: "leetcode-堆73"
date: 2026-08-21T09:54:15+08:00
tags: ["leetcode", "堆"]
draft: false
---


## 批量处理任务

某实验室计算机待处理任务以 [start,end,period] 格式记于二维数组 tasks，表示完成该任务的时间范围为起始时间 start 至结束时间 end 之间，需要计算机投入 period 的时长，注意：

1. period 可为不连续时间
2. 首尾时间均包含在内

处于开机状态的计算机可同时处理任意多个任务，请返回电脑最少开机多久，可处理完所有任务。


```
use std::collections::*;

impl Solution {
    pub fn process_tasks(tasks: Vec<Vec<i32>>) -> i32 {
        // 离散化所有时间点（使用左闭右开区间 [L, R)）
        let mut points = BTreeSet::new();
        for t in &tasks {
            points.insert(t[0]);
            points.insert(t[1] + 1); // +1 转为右开
        }
        let points: Vec<i32> = points.into_iter().collect();
        let m = points.len();

        // 按开始时间分组任务索引，避免 HashMap 和多次查询
        let mut by_start: Vec<Vec<usize>> = vec![vec![]; m];
        for (i, t) in tasks.iter().enumerate() {
            let idx = points.binary_search(&t[0]).unwrap();
            by_start[idx].push(i);
        }

        // 大根堆，存储 (剩余可拖延时间, 任务索引)
        // 剩余可拖延时间 = 截止时间 - 当前时间 + 1 - 所需时长
        // 取反实现小根堆效果
        let mut heap: BinaryHeap<(i32, usize)> = BinaryHeap::new();
        let mut extra = 0; // 堆中所有元素的补偿偏移量，避免逐个修改
        let mut ans = 0;

        for i in 0..m - 1 {
            let seg_len = points[i + 1] - points[i];

            // 移除已过期的任务
            while let Some(&(_, id)) = heap.peek() {
                if tasks[id][1] < points[i] {
                    heap.pop();
                } else {
                    break;
                }
            }

            // 将当前时间点开始的任务入堆
            for &id in &by_start[i] {
                let slack = tasks[id][1] - points[i] + 1 - tasks[id][2] + extra;
                heap.push((-slack, id));
            }

            if heap.is_empty() {
                continue;
            }

            // 当前最紧迫任务的剩余可拖延时间（扣除全局偏移）
            let min_slack = -heap.peek().unwrap().0 - extra;

            // 若当前区间长度超过最紧迫任务的剩余时间，必须开机
            if min_slack < seg_len {
                let need = seg_len - min_slack; // 本区间必须开机的时长
                ans += need;
                extra += seg_len - need;        // 剩余时长作为全局偏移
            } else {
                extra += seg_len;               // 整个区间都可拖延，全部加到偏移
            }
        }

        ans
    }
}
```

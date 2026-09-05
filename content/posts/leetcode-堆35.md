---
title: "leetcode-堆35"
date: 2026-08-21T09:54:12+08:00
tags: ["leetcode", "堆"]
draft: false
---


## 使用服务器处理任务

给你两个 下标从 0 开始 的整数数组 servers 和 tasks ，长度分别为 n 和 m 。servers[i] 是第 i 台服务器的 权重 ，而 tasks[j] 是处理第 j 项任务 所需要的时间（单位：秒）。

你正在运行一个仿真系统，在处理完所有任务后，该系统将会关闭。每台服务器只能同时处理一项任务。第 0 项任务在第 0 秒可以开始处理，相应地，第 j 项任务在第 j 秒可以开始处理。处理第 j 项任务时，你需要为它分配一台 权重最小 的空闲服务器。如果存在多台相同权重的空闲服务器，请选择 下标最小 的服务器。如果一台空闲服务器在第 t 秒分配到第 j 项任务，那么在 t + tasks[j] 时它将恢复空闲状态。

如果没有空闲服务器，则必须等待，直到出现一台空闲服务器，并 尽可能早 地处理剩余任务。 如果有多项任务等待分配，则按照 下标递增 的顺序完成分配。

如果同一时刻存在多台空闲服务器，可以同时将多项任务分别分配给它们。

构建长度为 m 的答案数组 ans ，其中 ans[j] 是第 j 项任务分配的服务器的下标。

返回答案数组 ans 。


```
use std::collections::BinaryHeap;
use std::cmp::Reverse;

impl Solution {
    pub fn assign_tasks(servers: Vec<i32>, tasks: Vec<i32>) -> Vec<i32> {
        let mut available: BinaryHeap<Reverse<(i32, usize)>> = servers
            .iter()
            .enumerate()
            .map(|(id, &w)| Reverse((w, id)))
            .collect();
        let mut busy: BinaryHeap<Reverse<(i64, usize)>> = BinaryHeap::new();

        let mut ans = Vec::with_capacity(tasks.len());
        let mut time: i64 = 0;

        for (task_id, &duration) in tasks.iter().enumerate() {
            time = time.max(task_id as i64);

            // Release all servers that have finished by now
            while let Some(&Reverse((t, id))) = busy.peek() {
                if t > time { break; }
                busy.pop();
                available.push(Reverse((servers[id], id)));
            }

            // If still no server, fast-forward to the next available one
            if available.is_empty() {
                if let Some(Reverse((t, id))) = busy.pop() {
                    time = t;
                    available.push(Reverse((servers[id], id)));
                }
                while let Some(&Reverse((t, id))) = busy.peek() {
                    if t != time { break; }
                    busy.pop();
                    available.push(Reverse((servers[id], id)));
                }
            }

            let Reverse((_, id)) = available.pop().unwrap();
            ans.push(id as i32);
            busy.push(Reverse((time + duration as i64, id)));
        }

        ans
    }
}
```

---
title: "leetcode-有序集合14"
date: 2026-06-14T10:47:41+08:00
tags: ["leetcode", "有序集合"]
draft: false
---


## 找到处理最多请求的服务器

你有 k 个服务器，编号为 0 到 k-1 ，它们可以同时处理多个请求组。每个服务器有无穷的计算能力但是 不能同时处理超过一个请求 。请求分配到服务器的规则如下：

· 第 i （序号从 0 开始）个请求到达。
· 如果所有服务器都已被占据，那么该请求被舍弃（完全不处理）。
· 如果第 (i % k) 个服务器空闲，那么对应服务器会处理该请求。
· 否则，将请求安排给下一个空闲的服务器（服务器构成一个环，必要的话可能从第 0 个服务器开始继续找下一个空闲的服务器）。比方说，如果第 i 个服务器在忙，那么会查看第 (i+1) 个服务器，第 (i+2) 个服务器等等。

给你一个 严格递增 的正整数数组 arrival ，表示第 i 个任务的到达时间，和另一个数组 load ，其中 load[i] 表示第 i 个请求的工作量（也就是服务器完成它所需要的时间）。你的任务是找到 最繁忙的服务器 。最繁忙定义为一个服务器处理的请求数是所有服务器里最多的。

请你返回包含所有 最繁忙服务器 序号的列表，你可以以任意顺序返回这个列表。


```
use std::collections::{BinaryHeap, BTreeSet};
use std::cmp::Reverse;
impl Solution {
    pub fn busiest_servers(k: i32, arrival: Vec<i32>, load: Vec<i32>) -> Vec<i32> {
        let k = k as usize;
        let mut counts = vec![0; k];
        let mut max_requests = 0;
        let mut available: BTreeSet<usize> = (0..k).collect();
        let mut busy: BinaryHeap<Reverse<(i32, usize)>> = BinaryHeap::new();

        for (i, (&arrival_time, &workload)) in arrival.iter().zip(load.iter()).enumerate() {
            let finish_time = arrival_time + workload;

            while let Some(&Reverse((end_time, server))) = busy.peek() {
                if end_time <= arrival_time {
                    busy.pop();
                    available.insert(server);
                } else {
                    break;
                }
            }

            if !available.is_empty() {
                let start = i % k;
                let server = available.range(start..).next()
                    .or_else(|| available.iter().next())
                    .copied()
                    .unwrap();

                available.remove(&server);
                busy.push(Reverse((finish_time, server)));

                counts[server] += 1;
                max_requests = max_requests.max(counts[server]);
            }
        }

        counts.iter().enumerate()
            .filter(|&(_, &count)| count == max_requests)
            .map(|(i, _)| i as i32)
            .collect()
    }
}
```

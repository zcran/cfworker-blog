---
title: "leetcode-图20"
date: 2026-07-27T10:01:36+08:00
tags: ["leetcode", "图"]
draft: false
---


## 网络空闲的时刻

给你一个有 n 个服务器的计算机网络，服务器编号为 0 到 n - 1 。同时给你一个二维整数数组 edges ，其中 edges[i] = [ui, vi] 表示服务器 ui 和 vi 之间有一条信息线路，在 一秒 内它们之间可以传输 任意 数目的信息。再给你一个长度为 n 且下标从 0 开始的整数数组 patience 。

题目保证所有服务器都是 相通 的，也就是说一个信息从任意服务器出发，都可以通过这些信息线路直接或间接地到达任何其他服务器。

编号为 0 的服务器是 主 服务器，其他服务器为 数据 服务器。每个数据服务器都要向主服务器发送信息，并等待回复。信息在服务器之间按 最优 线路传输，也就是说每个信息都会以 最少时间 到达主服务器。主服务器会处理 所有 新到达的信息并 立即 按照每条信息来时的路线 反方向 发送回复信息。

在 0 秒的开始，所有数据服务器都会发送各自需要处理的信息。从第 1 秒开始，每 一秒最 开始 时，每个数据服务器都会检查它是否收到了主服务器的回复信息（包括新发出信息的回复信息）：

如果还没收到任何回复信息，那么该服务器会周期性 重发 信息。数据服务器 i 每 patience[i] 秒都会重发一条信息，也就是说，数据服务器 i 在上一次发送信息给主服务器后的 patience[i] 秒 后 会重发一条信息给主服务器。

否则，该数据服务器 不会重发 信息。

当没有任何信息在线路上传输或者到达某服务器时，该计算机网络变为 空闲 状态。

请返回计算机网络变为 空闲 状态的 最早秒数 。


```
use std::collections::VecDeque;

impl Solution {
    pub fn network_becomes_idle(edges: Vec<Vec<i32>>, patience: Vec<i32>) -> i32 {
        let n = patience.len();

        // 构建邻接表
        let mut graph = vec![vec![]; n];
        for e in edges {
            let (u, v) = (e[0] as usize, e[1] as usize);
            graph[u].push(v);
            graph[v].push(u);
        }

        // BFS 计算每个节点到主服务器的最短距离
        let mut dist = vec![0; n];
        let mut visited = vec![false; n];
        let mut queue = VecDeque::new();

        queue.push_back(0);
        visited[0] = true;

        while let Some(u) = queue.pop_front() {
            for &v in &graph[u] {
                if !visited[v] {
                    visited[v] = true;
                    dist[v] = dist[u] + 1;
                    queue.push_back(v);
                }
            }
        }

        // 计算每个数据服务器最后一次发送和收到回复的时间
        let mut max_time = 0;
        for i in 1..n {
            let round_trip = 2 * dist[i]; // 往返时间
            let last_send = ((round_trip - 1) / patience[i]) * patience[i]; // 最后一次发送时间
            let finish_time = last_send + round_trip; // 收到回复并空闲的时间
            max_time = max_time.max(finish_time);
        }

        // 空闲时间 = 最后一个数据服务器空闲时间 + 1（秒数从 1 开始计数）
        max_time + 1
    }
}
```

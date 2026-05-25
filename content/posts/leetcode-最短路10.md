---
title: "leetcode-最短路10"
date: 2026-04-22T20:45:20+08:00
tags: ["leetcode", "最短路"]
draft: false
---


## 到达目的地的第二短时间

城市用一个 双向连通 图表示，图中有 n 个节点，从 1 到 n 编号（包含 1 和 n）。图中的边用一个二维整数数组 edges 表示，其中每个 edges[i] = [ui, vi] 表示一条节点 ui 和节点 vi 之间的双向连通边。每组节点对由 最多一条 边连通，顶点不存在连接到自身的边。穿过任意一条边的时间是 time 分钟。

每个节点都有一个交通信号灯，每 change 分钟改变一次，从绿色变成红色，再由红色变成绿色，循环往复。所有信号灯都 同时 改变。你可以在 任何时候 进入某个节点，但是 只能 在节点 信号灯是绿色时 才能离开。如果信号灯是  绿色 ，你 不能 在节点等待，必须离开。

第二小的值 是 严格大于 最小值的所有值中最小的值。

例如，[2, 3, 4] 中第二小的值是 3 ，而 [2, 2, 4] 中第二小的值是 4 。
给你 n、edges、time 和 change ，返回从节点 1 到节点 n 需要的 第二短时间 。

注意：

你可以 任意次 穿过任意顶点，包括 1 和 n 。
你可以假设在 启程时 ，所有信号灯刚刚变成 绿色 。


```
use std::collections::VecDeque;

const INF: i32 = i32::MAX;

impl Solution {
    /// 计算从节点 1 到节点 n 的**第二短时间**（严格大于最短时间）。
    /// BFS 分层扩展，每层代表经过相同边数的路径。
    /// 每条边行走耗时 `time`，红绿灯影响离开节点的时刻（每层结束后检查等待）。
    pub fn second_minimum(n: i32, edges: Vec<Vec<i32>>, time: i32, change: i32) -> i32 {
        let n = n as usize;
        let target = n;

        // ---------- 邻接表 ----------
        let mut graph = vec![vec![]; n + 1];
        for edge in edges {
            let u = edge[0] as usize;
            let v = edge[1] as usize;
            graph[u].push(v);
            graph[v].push(u);
        }

        // dist1[node] = 最短到达时间, dist2[node] = 次短到达时间
        let mut dist1 = vec![INF; n + 1];
        let mut dist2 = vec![INF; n + 1];
        dist1[1] = 0;

        let mut queue = VecDeque::new();
        queue.push_back(1);
        let mut current_time = 0; // 到达当前层节点的时刻（未考虑等待）

        while !queue.is_empty() && dist2[target] == INF {
            // 1) 从当前层所有节点出发，行走 time 秒到达邻居
            current_time += time;

            // 2) 处理当前层内所有节点
            let level_size = queue.len();
            for _ in 0..level_size {
                let u = queue.pop_front().unwrap();
                for &v in &graph[u] {
                    if dist1[v] == INF {
                        dist1[v] = current_time;
                        queue.push_back(v);
                    } else if dist2[v] == INF && current_time != dist1[v] {
                        dist2[v] = current_time;
                        queue.push_back(v);
                    }
                }
            }

            // 3) 处理整层结束后的红绿灯等待（只影响下一层的出发时间）
            if (current_time / change) % 2 == 1 {
                current_time += change - (current_time % change);
            }
        }

        if dist2[target] == INF { -1 } else { dist2[target] }
    }
}
```

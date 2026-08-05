---
title: "leetcode-图8"
date: 2026-07-27T10:01:35+08:00
tags: ["leetcode", "图"]
draft: false
---


## 颜色交替的最短路径

给定一个整数 n，即有向图中的节点数，其中节点标记为 0 到 n - 1。图中的每条边为红色或者蓝色，并且可能存在自环或平行边。

给定两个数组 redEdges 和 blueEdges，其中：

redEdges[i] = [ai, bi] 表示图中存在一条从节点 ai 到节点 bi 的红色有向边，

blueEdges[j] = [uj, vj] 表示图中存在一条从节点 uj 到节点 vj 的蓝色有向边。

返回长度为 n 的数组 answer，其中 answer[X] 是从节点 0 到节点 X 的红色边和蓝色边交替出现的最短路径的长度。如果不存在这样的路径，那么 answer[x] = -1。


```
impl Solution {
    pub fn shortest_alternating_paths(n: i32, red_edges: Vec<Vec<i32>>, blue_edges: Vec<Vec<i32>>) -> Vec<i32> {
        let n = n as usize;

        // 构建邻接表：red[i] 存储从 i 出发的红色边目标，blue[i] 存储蓝色边目标
        let mut red = vec![vec![]; n];
        let mut blue = vec![vec![]; n];

        for e in red_edges {
            red[e[0] as usize].push(e[1] as usize);
        }
        for e in blue_edges {
            blue[e[0] as usize].push(e[1] as usize);
        }

        // dist[node][color]: 到达 node 且最后一条边颜色为 color 的最短距离
        // color: 0 表示红色，1 表示蓝色
        let mut dist = vec![[i32::MAX; 2]; n];
        dist[0] = [0, 0];

        use std::collections::VecDeque;
        let mut queue = VecDeque::new();
        queue.push_back((0, 0)); // (节点, 上次边颜色) 初始可视为红色
        queue.push_back((0, 1)); // 初始可视为蓝色

        while let Some((node, last_color)) = queue.pop_front() {
            let next_color = 1 - last_color; // 交替颜色
            let neighbors = if next_color == 0 { &red[node] } else { &blue[node] };
            let next_dist = dist[node][last_color] + 1;

            for &next in neighbors {
                if dist[next][next_color] > next_dist {
                    dist[next][next_color] = next_dist;
                    queue.push_back((next, next_color));
                }
            }
        }

        // 取两种颜色到达每个节点的最小值
        (0..n)
            .map(|i| {
                let d = dist[i][0].min(dist[i][1]);
                if d == i32::MAX { -1 } else { d }
            })
            .collect()
    }
}
```

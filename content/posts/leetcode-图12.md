---
title: "leetcode-图12"
date: 2026-07-27T10:01:35+08:00
tags: ["leetcode", "图"]
draft: false
---


##  T 秒后青蛙的位置

给你一棵由 n 个顶点组成的无向树，顶点编号从 1 到 n。青蛙从 顶点 1 开始起跳。规则如下：

· 在一秒内，青蛙从它所在的当前顶点跳到另一个 未访问 过的顶点（如果它们直接相连）。
· 青蛙无法跳回已经访问过的顶点。
· 如果青蛙可以跳到多个不同顶点，那么它跳到其中任意一个顶点上的机率都相同。
· 如果青蛙不能跳到任何未访问过的顶点上，那么它每次跳跃都会停留在原地。

无向树的边用数组 edges 描述，其中 edges[i] = [ai, bi] 意味着存在一条直接连通 ai 和 bi 两个顶点的边。

返回青蛙在 t 秒后位于目标顶点 target 上的概率。与实际答案相差不超过 10-5 的结果将被视为正确答案。


```
use std::collections::VecDeque;

impl Solution {
    pub fn frog_position(n: i32, edges: Vec<Vec<i32>>, t: i32, target: i32) -> f64 {
        let n = n as usize;
        let target = target as usize;
        let t = t as usize;

        // 构建邻接表
        let mut graph = vec![vec![]; n + 1];
        for e in edges {
            let (u, v) = (e[0] as usize, e[1] as usize);
            graph[u].push(v);
            graph[v].push(u);
        }

        // BFS 逐层计算概率
        let mut queue = VecDeque::new();
        let mut visited = vec![false; n + 1];

        queue.push_back((1, 1.0)); // (节点, 到达该节点的概率)
        visited[1] = true;
        let mut time = 0;

        while time <= t && !queue.is_empty() {
            let size = queue.len();

            for _ in 0..size {
                let (u, prob) = queue.pop_front().unwrap();

                // 计算下一步可跳的未访问邻居数
                let mut next_count = 0;
                for &v in &graph[u] {
                    if !visited[v] {
                        next_count += 1;
                    }
                }

                // 到达目标：如果不能再跳或者时间耗尽
                if u == target {
                    // 如果还有未访问邻居且时间未到，青蛙不会停在这里
                    if next_count > 0 && time < t {
                        // 继续处理其他节点，但不返回
                    } else {
                        return prob;
                    }
                }

                // 如果时间耗尽，不再扩展
                if time == t {
                    continue;
                }

                // 跳到每个未访问邻居
                if next_count > 0 {
                    let next_prob = prob / next_count as f64;
                    for &v in &graph[u] {
                        if !visited[v] {
                            visited[v] = true;
                            queue.push_back((v, next_prob));
                        }
                    }
                }
                // 如果没有未访问邻居，青蛙原地跳跃，但概率不变，无需入队
            }

            time += 1;
        }

        // 无法在 t 秒内到达 target
        0.0
    }
}
```

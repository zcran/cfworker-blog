---
title: "leetcode-广度优先搜索34"
date: 2026-09-13T09:32:08+08:00
tags: ["leetcode", "广度优先搜索"]
draft: false
---


## 使陆地分离的最少天数

给你一个大小为 m x n ，由若干 0 和 1 组成的二维网格 grid ，其中 1 表示陆地， 0 表示水。岛屿 由水平方向或竖直方向上相邻的 1 （陆地）连接形成。

如果 恰好只有一座岛屿 ，则认为陆地是 连通的 ；否则，陆地就是 分离的 。

一天内，可以将 任何单个 陆地单元（1）更改为水单元（0）。

返回使陆地分离的最少天数。


```
impl Solution {
    pub fn min_days(grid: Vec<Vec<i32>>) -> i32 {
        let (m, n) = (grid.len(), grid[0].len());

        // 1. 给每块陆地编号，便于建图
        let mut relabel = vec![vec![0usize; n]; m];
        let mut count = 0;
        for i in 0..m {
            for j in 0..n {
                if grid[i][j] == 1 {
                    relabel[i][j] = count;
                    count += 1;
                }
            }
        }

        // 陆地数量 <= 1：已经是分离状态（0 或 1 块），无需操作
        if count <= 1 {
            return count as i32;
        }

        // 2. 构建陆地邻接图
        let mut graph = vec![Vec::new(); count];
        for i in 0..m {
            for j in 0..n {
                if grid[i][j] != 1 {
                    continue;
                }
                let u = relabel[i][j];
                if i + 1 < m && grid[i + 1][j] == 1 {
                    let v = relabel[i + 1][j];
                    graph[u].push(v);
                    graph[v].push(u);
                }
                if j + 1 < n && grid[i][j + 1] == 1 {
                    let v = relabel[i][j + 1];
                    graph[u].push(v);
                    graph[v].push(u);
                }
            }
        }

        // 3. 判断：已分离 → 0；有割点 → 1；否则 → 2
        Solver::new(graph).solve()
    }
}

/// Tarjan 求割点
struct Solver {
    timestamp: usize,
    graph: Vec<Vec<usize>>,
    dfn: Vec<usize>,
    low: Vec<usize>,
    cut_points: Vec<usize>,
}

impl Solver {
    fn new(graph: Vec<Vec<usize>>) -> Self {
        let n = graph.len();
        Self {
            timestamp: 1,
            dfn: vec![0; n],
            low: vec![0; n],
            graph,
            cut_points: Vec::new(),
        }
    }

    fn solve(&mut self) -> i32 {
        // 从节点 0 开始 DFS（用于判断连通性和找割点）
        self.dfs(0, usize::MAX);

        // 若 DFS 未访问到所有节点，说明本来就有多座岛 → 0 天
        if self.dfn.iter().any(|&d| d == 0) {
            return 0;
        }

        // 存在割点 → 1 天
        if !self.cut_points.is_empty() {
            return 1;
        }

        // 否则最多 2 天（总能通过删两个格子把图断开）
        2
    }

    fn dfs(&mut self, cur: usize, parent: usize) {
        self.dfn[cur] = self.timestamp;
        self.low[cur] = self.timestamp;
        self.timestamp += 1;

        let mut children = 0;
        let mut is_cut = false;

        // 克隆邻接表，避免遍历时同时可变借用 self
        let neighbors = self.graph[cur].clone();

        for next in neighbors {
            if next == parent {
                continue;
            }
            if self.dfn[next] == 0 {
                // 未访问：递归并更新 low
                children += 1;
                self.dfs(next, cur);
                self.low[cur] = self.low[cur].min(self.low[next]);
                // 非根节点：子节点无法回溯到 cur 之上 → cur 是割点
                if !is_cut && parent != usize::MAX && self.low[next] >= self.dfn[cur] {
                    is_cut = true;
                    self.cut_points.push(cur);
                }
            } else {
                // 已访问且不是父节点：用 dfn 更新 low
                self.low[cur] = self.low[cur].min(self.dfn[next]);
            }
        }

        // 根节点：有 ≥ 2 棵子树 → 是割点
        if !is_cut && parent == usize::MAX && children >= 2 {
            self.cut_points.push(cur);
        }
    }
}
```

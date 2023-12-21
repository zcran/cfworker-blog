---
title: "Leetcode 强连通分量1"
date: 2023-12-19T15:20:40+08:00
tags: ["leetcode", "强连通分量"]
draft: false
---

## 使陆地分离的最少天数

给你一个大小为 m x n ，由若干 0 和 1 组成的二维网格 grid ，其中 1 表示陆地， 0 表示水。岛屿 由水平方向或竖直方向上相邻的 1 （陆地）连接形成。

如果 恰好只有一座岛屿 ，则认为陆地是 连通的 ；否则，陆地就是 分离的 。

一天内，可以将 任何单个 陆地单元（1）更改为水单元（0）。

返回使陆地分离的最少天数。

示例1:
输入：grid = [[0,1,1,0],[0,1,1,0],[0,0,0,0]]
输出：2
解释：至少需要 2 天才能得到分离的陆地。
将陆地 grid[1][1] 和 grid[0][2] 更改为水，得到两个分离的岛屿。

示例2:
输入：grid = [[1,1]]
输出：2
解释：如果网格中都是水，也认为是分离的 ([[1,1]] -> [[0,0]])，0 岛屿。

```
impl Solution {
    pub fn min_days(grid: Vec<Vec<i32>>) -> i32 {
        let (m, n) = (grid.len(), grid[0].len());

        let mut relabel = vec![vec![0; n]; m];
        let mut idx = 0;
        for i in 0..m {
            for j in 0..n {
                if grid[i][j] == 1 {
                    relabel[i][j] = idx;
                    idx += 1;
                }
            }
        }

        if idx <= 1 {
            return idx as i32;
        }

        let f = |i: usize, j: usize| relabel[i][j];
        let mut graph = vec![vec![]; idx];
        for i in 0..m {
            for j in 0..n {
                if grid[i][j] == 1 {
                    if i < m - 1 && grid[i + 1][j] == 1 {
                        graph[f(i, j)].push(f(i + 1, j));
                        graph[f(i + 1, j)].push(f(i, j));
                    }
                    if j < n - 1 && grid[i][j + 1] == 1 {
                        graph[f(i, j)].push(f(i, j + 1));
                        graph[f(i, j + 1)].push(f(i, j));
                    }
                }
            }
        }
        let mut s = Solver::new(graph);
        s.solve()
    }
}

struct Solver {
    timestamp: usize,
    graph: Vec<Vec<usize>>,
    dfn: Vec<usize>,
    low: Vec<usize>,
    cut_points: Vec<usize>,
}

impl Solver {
    fn new(graph: Vec<Vec<usize>>) -> Self {
        Self {
            timestamp: 1,
            dfn: vec![0; graph.len()],
            low: vec![0; graph.len()],
            graph,
            cut_points: vec![],
        }
    }

    fn solve(&mut self) -> i32 {
        self.dfs(0, self.graph.len());

        let mut ans = 2;
        if !self.cut_points.is_empty() {
            ans = 1;
        }
        for i in 0..self.graph.len() {
            if self.dfn[i] == 0 {
                return 0;
            }
        }
        ans
    }

    fn dfs(&mut self, cur: usize, pre: usize) {
        self.dfn[cur] = self.timestamp;
        self.low[cur] = self.timestamp;
        self.timestamp += 1;

        let mut child = 0;
        let mut is_cut = false;
        
        for i in 0..self.graph[cur].len() {
            let nxt = self.graph[cur][i];
            if nxt == pre {
                continue;
            }
            if self.dfn[nxt] == 0 {
                child += 1;
                self.dfs(nxt, cur);
                if !is_cut && pre != self.graph.len() && self.low[nxt] >= self.dfn[cur] {
                    is_cut = true;
                    self.cut_points.push(cur);
                }
            }
            self.low[cur] = self.low[cur].min(self.low[nxt]);
        }

        if !is_cut && pre == self.graph.len() && child >= 2 {
            self.cut_points.push(cur);
        }
    }
}
```
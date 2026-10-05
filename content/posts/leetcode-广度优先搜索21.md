---
title: "leetcode-广度优先搜索21"
date: 2026-09-13T09:32:07+08:00
tags: ["leetcode", "广度优先搜索"]
draft: false
---


## 最短的桥

给你一个大小为 n x n 的二元矩阵 grid ，其中 1 表示陆地，0 表示水域。

岛 是由四面相连的 1 形成的一个最大组，即不会与非组内的任何其他 1 相连。grid 中 恰好存在两座岛 。

你可以将任意数量的 0 变为 1 ，以使两座岛连接起来，变成 一座岛 。

返回必须翻转的 0 的最小数目。


```
impl Solution {
    pub fn shortest_bridge(grid: Vec<Vec<i32>>) -> i32 {
        use std::collections::VecDeque;

        let n = grid.len();
        let mut dist = vec![vec![-1i32; n]; n]; // -1 表示未访问
        let mut q = VecDeque::new();

        // 1. 找到第一座岛，并把它所有格子入队（多源 BFS 起点）
        'find_first: for i in 0..n {
            for j in 0..n {
                if grid[i][j] == 1 {
                    dist[i][j] = 0;
                    q.push_back((i, j));
                    break 'find_first;
                }
            }
        }

        // 先把第一座岛的所有格子标记为距离 0，并全部入队
        // 用 DFS 或 BFS 扩展同岛的格子
        let mut head = 0;
        while head < q.len() {
            let (x, y) = q[head];
            head += 1;
            // 四方向扩展：如果是陆地且未访问，则同岛，距离保持 0
            if x > 0 && grid[x - 1][y] == 1 && dist[x - 1][y] == -1 {
                dist[x - 1][y] = 0;
                q.push_back((x - 1, y));
            }
            if x + 1 < n && grid[x + 1][y] == 1 && dist[x + 1][y] == -1 {
                dist[x + 1][y] = 0;
                q.push_back((x + 1, y));
            }
            if y > 0 && grid[x][y - 1] == 1 && dist[x][y - 1] == -1 {
                dist[x][y - 1] = 0;
                q.push_back((x, y - 1));
            }
            if y + 1 < n && grid[x][y + 1] == 1 && dist[x][y + 1] == -1 {
                dist[x][y + 1] = 0;
                q.push_back((x, y + 1));
            }
        }

        // 2. 从第一座岛出发做 BFS，遇到第二座岛的陆地即返回距离
        while let Some((x, y)) = q.pop_front() {
            let d = dist[x][y];

            // 四方向扩展
            let mut visit = |nx: usize, ny: usize, q: &mut VecDeque<(usize, usize)>| -> bool {
                if dist[nx][ny] != -1 {
                    return false;
                }
                if grid[nx][ny] == 1 {
                    // 遇到第二座岛
                    return true;
                }
                dist[nx][ny] = d + 1;
                q.push_back((nx, ny));
                false
            };

            if x > 0 && visit(x - 1, y, &mut q) { return d; }
            if x + 1 < n && visit(x + 1, y, &mut q) { return d; }
            if y > 0 && visit(x, y - 1, &mut q) { return d; }
            if y + 1 < n && visit(x, y + 1, &mut q) { return d; }
        }

        unreachable!()
    }
}
```

---
title: "leetcode-广度优先搜索44"
date: 2026-09-13T09:32:09+08:00
tags: ["leetcode", "广度优先搜索"]
draft: false
---


## 网格传送门旅游

给你一个大小为 m x n 的二维字符网格 matrix，用字符串数组表示，其中 matrix[i][j] 表示第 i 行和第 j 列处的单元格。每个单元格可以是以下几种字符之一：

'.' 表示一个空单元格。
'#' 表示一个障碍物。
一个大写字母（'A' 到 'Z'）表示一个传送门。

你从左上角单元格 (0, 0) 出发，目标是到达右下角单元格 (m - 1, n - 1)。你可以从当前位置移动到相邻的单元格（上、下、左、右），移动后的单元格必须在网格边界内且不是障碍物。

如果你踏入一个包含传送门字母的单元格，并且你之前没有使用过该传送门字母，你可以立即传送到网格中另一个具有相同字母的单元格。这次传送不计入移动次数，但每个字母对应的传送门在旅程中 最多 只能使用一次。

返回到达右下角单元格所需的 最少 移动次数。如果无法到达目的地，则返回 -1。


```
use std::collections::VecDeque;

impl Solution {
    pub fn min_moves(matrix: Vec<String>) -> i32 {
        let m = matrix.len();
        let n = matrix[0].len();
        let grid: Vec<Vec<u8>> = matrix.iter().map(|s| s.as_bytes().to_vec()).collect();

        // 终点是障碍物，直接无解
        if grid[m - 1][n - 1] == b'#' {
            return -1;
        }

        // 收集每个字母对应传送门的所有坐标
        let mut portals: Vec<Vec<(usize, usize)>> = vec![Vec::new(); 26];
        for i in 0..m {
            for j in 0..n {
                let c = grid[i][j];
                if c.is_ascii_uppercase() {
                    portals[(c - b'A') as usize].push((i, j));
                }
            }
        }

        let mut dist = vec![vec![i32::MAX; n]; m];
        dist[0][0] = 0;

        // 0-1 BFS：传送门不花代价（push_front），普通移动花费 1（push_back）
        let mut queue = VecDeque::new();
        queue.push_back((0usize, 0usize));

        const DIRS: [(isize, isize); 4] = [(0, 1), (0, -1), (1, 0), (-1, 0)];

        while let Some((x, y)) = queue.pop_front() {
            let d = dist[x][y];

            if x == m - 1 && y == n - 1 {
                return d;
            }

            // 若当前格是传送门字母，则免费传送到同字母的其他所有格子
            let c = grid[x][y];
            if c.is_ascii_uppercase() {
                let idx = (c - b'A') as usize;
                for &(px, py) in &portals[idx] {
                    if d < dist[px][py] {
                        dist[px][py] = d;
                        queue.push_front((px, py));
                    }
                }
                // 每个字母的传送门只用一次，用完清空避免重复遍历
                portals[idx].clear();
            }

            // 普通四方向移动
            for &(dx, dy) in &DIRS {
                let nx = x as isize + dx;
                let ny = y as isize + dy;
                if nx < 0 || ny < 0 || nx >= m as isize || ny >= n as isize {
                    continue;
                }
                let (nx, ny) = (nx as usize, ny as usize);
                if grid[nx][ny] != b'#' && d + 1 < dist[nx][ny] {
                    dist[nx][ny] = d + 1;
                    queue.push_back((nx, ny));
                }
            }
        }

        -1
    }
}
```

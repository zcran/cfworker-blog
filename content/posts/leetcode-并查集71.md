---
title: "leetcode-并查集71"
date: 2026-06-25T11:22:24+08:00
tags: ["leetcode", "并查集"]
draft: false
---


## 主题空间


「以扣会友」线下活动所在场地由若干主题空间与走廊组成，场地的地图记作由一维字符串型数组 grid，字符串中仅包含 "0"～"5" 这 6 个字符。地图上每一个字符代表面积为 1 的区域，其中 "0" 表示走廊，其他字符表示主题空间。相同且连续（连续指上、下、左、右四个方向连接）的字符组成同一个主题空间。

假如整个 grid 区域的外侧均为走廊。请问，不与走廊直接相邻的主题空间的最大面积是多少？如果不存在这样的空间请返回 0。

```
use std::collections::VecDeque;

impl Solution {
    /// 计算不与走廊直接相邻的最大主题空间面积
    pub fn largest_area(grid: Vec<String>) -> i32 {
        let rows = grid.len();
        let cols = grid[0].len();
        let mut visited = vec![vec![false; cols]; rows];
        let mut max_area = 0;

        // 四个方向：上、下、左、右
        const DIRS: [(i32, i32); 4] = [(-1, 0), (1, 0), (0, -1), (0, 1)];

        for i in 0..rows {
            for j in 0..cols {
                let ch = grid[i].as_bytes()[j];
                if ch != b'0' && !visited[i][j] {
                    let (area, touches_boundary) = Self::bfs(&grid, &mut visited, i, j, ch, &DIRS);
                    if !touches_boundary {
                        max_area = max_area.max(area);
                    }
                }
            }
        }

        max_area
    }

    /// BFS 搜索同主题空间，返回（面积，是否与走廊/边界相邻）
    fn bfs(
        grid: &[String],
        visited: &mut [Vec<bool>],
        start_i: usize,
        start_j: usize,
        target: u8,
        dirs: &[(i32, i32); 4],
    ) -> (i32, bool) {
        let mut queue = VecDeque::new();
        queue.push_back((start_i, start_j));
        visited[start_i][start_j] = true;

        let mut area = 0;
        let mut touches_boundary = false;

        while let Some((i, j)) = queue.pop_front() {
            area += 1;

            for &(di, dj) in dirs {
                let ni = i as i32 + di;
                let nj = j as i32 + dj;

                // 检查是否越界或遇到走廊（'0'）
                if ni < 0 || ni >= grid.len() as i32 || nj < 0 || nj >= grid[0].len() as i32
                    || grid[ni as usize].as_bytes()[nj as usize] == b'0'
                {
                    touches_boundary = true;
                    continue;
                }

                let ni = ni as usize;
                let nj = nj as usize;
                if !visited[ni][nj] && grid[ni].as_bytes()[nj] == target {
                    visited[ni][nj] = true;
                    queue.push_back((ni, nj));
                }
            }
        }

        (area, touches_boundary)
    }
}
```

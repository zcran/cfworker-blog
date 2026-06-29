---
title: "leetcode-并查集28"
date: 2026-06-25T11:22:22+08:00
tags: ["leetcode", "并查集"]
draft: false
---


## 检查网格中是否存在有效路径

给你一个 m x n 的网格 grid。网格里的每个单元都代表一条街道。grid[i][j] 的街道可以是：

1 表示连接左单元格和右单元格的街道。
2 表示连接上单元格和下单元格的街道。
3 表示连接左单元格和下单元格的街道。
4 表示连接右单元格和下单元格的街道。
5 表示连接左单元格和上单元格的街道。
6 表示连接右单元格和上单元格的街道。

你最开始从左上角的单元格 (0,0) 开始出发，网格中的「有效路径」是指从左上方的单元格 (0,0) 开始、一直到右下方的 (m-1,n-1) 结束的路径。该路径必须只沿着街道走。

注意：你 不能 变更街道。

如果网格中存在有效的路径，则返回 true，否则返回 false 。

```
impl Solution {
    /// 判断网格中是否存在从左上到右下的有效路径。
    /// 使用 DFS 沿着街道方向行走，方向由当前格子的街道类型决定。
    pub fn has_valid_path(grid: Vec<Vec<i32>>) -> bool {
        let m = grid.len();
        let n = grid[0].len();

        // 街道类型对应的可行方向：(行偏移, 列偏移)
        // 方向编码: 0=上, 1=右, 2=下, 3=左
        const DIRS: [[(i32, i32); 2]; 7] = [
            [(0, 0), (0, 0)], // 0: 无效（占位）
            [(0, 1), (0, -1)], // 1: 左 ↔ 右
            [(1, 0), (-1, 0)], // 2: 上 ↔ 下
            [(0, -1), (1, 0)], // 3: 左 ↔ 下
            [(0, 1), (1, 0)],  // 4: 右 ↔ 下
            [(0, -1), (-1, 0)],// 5: 左 ↔ 上
            [(0, 1), (-1, 0)], // 6: 右 ↔ 上
        ];

        // 判断从某个方向进入格子是否合法
        // 入口方向编码: 0=上, 1=右, 2=下, 3=左
        const ENTRY: [[bool; 4]; 7] = [
            [false, false, false, false], // 0: 无效
            [false, true, false, true],   // 1: 可从右(1)/左(3)进入
            [true, false, true, false],   // 2: 可从上(0)/下(2)进入
            [false, false, true, true],   // 3: 可从下(2)/左(3)进入
            [false, true, true, false],   // 4: 可从右(1)/下(2)进入
            [true, false, false, true],   // 5: 可从上(0)/左(3)进入
            [true, true, false, false],   // 6: 可从上(0)/右(1)进入
        ];

        let mut visited = vec![vec![false; n]; m];
        let mut stack = vec![(0, 0)];
        visited[0][0] = true;

        // 方向向量到编码的映射
        let dir_to_code = |dr: i32, dc: i32| -> usize {
            match (dr, dc) {
                (-1, 0) => 0, // 上
                (0, 1) => 1,  // 右
                (1, 0) => 2,  // 下
                (0, -1) => 3, // 左
                _ => unreachable!(),
            }
        };

        while let Some((r, c)) = stack.pop() {
            if r == m - 1 && c == n - 1 {
                return true;
            }

            let street = grid[r][c] as usize;
            for &(dr, dc) in &DIRS[street] {
                if dr == 0 && dc == 0 {
                    continue; // 跳过占位
                }

                let nr = r as i32 + dr;
                let nc = c as i32 + dc;
                if nr < 0 || nr >= m as i32 || nc < 0 || nc >= n as i32 {
                    continue;
                }
                let (nr, nc) = (nr as usize, nc as usize);
                if visited[nr][nc] {
                    continue;
                }

                // 计算邻居的入方向：即从邻居指向当前的方向
                // 当前移动方向是 (dr, dc)，其反向就是邻居的入方向
                let dir = dir_to_code(dr, dc);
                let entry_dir = (dir + 2) % 4; // 反向
                let neighbor_street = grid[nr][nc] as usize;
                if ENTRY[neighbor_street][entry_dir] {
                    visited[nr][nc] = true;
                    stack.push((nr, nc));
                }
            }
        }

        false
    }
}
```
